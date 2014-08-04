---
layout: post
title: Javascript中，为什么既有call又有apply
tags:
  - JavaScript
date: 2012-05-10 21:47:47
---

Javascript中，每个函数都有这两个方法，`call`和`apply`，它们之间的区别很小：call中参数是直接写的，而apply中要放在一个数组（或跟数组很像但不同数组的对象）里。

有关两者区别的更详细资料，请参考：[http://stackoverflow.com/questions/1986896/what-is-the-difference-between-call-and-apply](http://stackoverflow.com/questions/1986896/what-is-the-difference-between-call-and-apply)

这里举个例子：

> function hello(name, message) {     
> alert(name + ': ' + message);      
> };      
> hello.call(null, 'Freewind', 'call');      
> hello.apply(null, ['Freewind', 'apply']);​​

运行的结果是一模一样的。

问题来了：**既然两者之间的差别这么小，为什么要同时提供这两个函数？一个不够用吗？**

答案是：**apply是为arguments而生的。**

arguments是函数中预定义的一个变量，它表示实际传递给该函数的参数。它不是数组，但是跟数组很像。看这个例子：

> function f(a,b,c) {     
> alert(arguments.length + ": " + join(arguments));      
> }      
> function join(args) {      
> var s = &#8221;;      
> for(i in args) {      
> s += args[i] + ' ';      
> }      
> return s;      
> }      
> f(10);      
> f(10,20);      
> f(10,20,30);      
> f(10,20,30,40);      
> ​

可见，arguments中的确保存了全部传入的参数，不管实际传入的参数是多还是少。

如果我们想在一个函数中，把所有传入的参数，原封不动的转给另一个函数，只能靠apply了。如果你用call，就必须显式地把参数一个个写上去，但这是不可能的，因为你根本不能确定调用者到底传了多少个参数过来。

> function f1(a,b,c) { f2.apply(null, arguments); }     
> function f2(a,b,c,d) { alert(a+','+b+','+c+','+d);}      
> f1('11&#8242;,'22&#8242;,'33&#8242;,'44&#8242;);​​​​​

那我们只要apply就够了，为什么还要call呢？答案是，call写起来更简洁明了。当参数确定时，使用call写起来更快更顺手（不用写那一对中括号了）。

以上是我浅显的理解，欢迎补充