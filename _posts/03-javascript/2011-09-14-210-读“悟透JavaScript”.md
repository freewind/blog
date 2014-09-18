---
layout: post
id: 210
alias: notes-of-book-understand-javascript-completely
tags: JavaScript
date: 2011-09-14 01:34:06
title: 读“悟透JavaScript”
---

学习JavaScript已经成为我无法逃避的问题。我发现，一个网站的开发时间，前端比后端用的还多，主要是因为组件不易复用、跨浏览器问题。解决这一问题的方法是，可以采用一些JavaScript UI库，如Jquery-UI, YUI, Ext-js, Qooxdoo, SmartClient, Ukijs等等。其中Ukijs我尤其推荐。

我以前看过国人李战写的一些文章，叫“悟透JavaScript”，不长，但是写得非常好，后来出版了。当时觉得不错，现在有些地方模糊了，又把它找出来重读。这本书是我最为推荐的一本JavaScript书籍，任何一个想学JavaScript的人，都应该先认真地把这本书看一遍，然后再看别的。

现在记得的几个要点是：

1.  放下“类型”。JavaScript是动态语言，所以变量没有类型。这可以做出很多静态语言难以做到的事情，比如动态改变类，增删属性或方法，把数据当作代码执行等。
2.  放下“自我”。function中的this，跟Java中的this不一样，并不总是指向自己。我们可以通过fun.call(other)，将other传进去，变成fun中的this。这样看来，javascript中的函数与groovy中的闭包相当相似。
3.  prototype继承。与面向对象语言中的继承不同，JavaScript用的是prototype。每一个类都有一个特殊的属性叫prototype，该类所有的对象共用它。当调用一个方法而本身没有时，则去prototype去找。利用它，我们可以设计出父类和子类的继承关系，即让子类的prototype属性指向父类。
4.  甘露模型。以实例说明，如何利用一个公用函数，优雅的模拟出面向对象语言中的继承语法，需要好好体会。

看完这本书，我有种“听君一席话，胜读十年书”的感觉。只花短短几个小时，便学到了自学数月也难以理解到的JavaScript原理，真不愧“悟透”两字。像李战这样的作者，才算得上是深入浅出的高手。
