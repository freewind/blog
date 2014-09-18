---
layout: post
id: 18
alias: use-lambdaj-to-use-lambda-in-java
tags: Java
date: 2011-09-12 23:41:04
title: 在java中使用闭包 – lambdaj
---

在闭包如此流行的时代，java却迟迟不能支持闭包，不能不说是一件让人失望郁闷的事情。Java7也不支持，Java8也许吧，但不知道那是多久之后的事情（或者只支持lambda?）
<p>终于出现了一个叫lambdaj的项目，它充分使用了java的静态导入等功能，模拟出来了一个闭包系统。先看一行代码：
<p>List<Integer> biggerThan3 = filter(greaterThan(3), asList(1, 2, 3, 4, 5));

这是从一个列表中，找到所有大于3的数字。这种写法，是不是挺新奇的？
<p>地址：[http://code.google.com/p/lambdaj/](http://code.google.com/p/lambdaj/)
<p>仔细看了一下lambdaj，想把它的用法弄熟，运用到自己的项目里。细读之下，发现这个东西还是不太好用。只有小部分的代码看起来很清晰且符合习惯，大多数复杂的用法都还是比较别扭和怪异。毕竟java本身语法上的限制，导致lambdaj只能通过一些扭曲的方式来模拟，效果就像是把一件花肚兜套在了java大叔笔挺的西装外面。风格的不相配会让代码写起来异常别扭。
<p>如果真的喜欢这些，不妨直接转向scala，用起来感觉会爽得多。
