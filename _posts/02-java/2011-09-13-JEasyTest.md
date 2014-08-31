---
layout: post
id: 91
alias: try-jeasytest
tags: Java
date: 2011-09-13 01:41:19
title: JEasyTest
---

曾经，所有的mock库都有这样的缺陷：不能mock静态方法，不能mock final方法，不能mock new出来的对象。所以，你的代码中，总是有一些地方是无法测试的，测试覆盖率总是无法达到100%。
<p>但是，JEasyTest出现了。这个家伙，可以在你的测试代码运行前，通过AOP的方式，改变那些依赖的类的字节码。哪怕是静态函数，final函数，以及new方式创建对象时所调用的函数，都可以被改变，返回你所希望返回的内容。这样，以前无法测试的内容，都可以被测试了，太厉害了。
<p>JEasyTest提供了一个eclipse的插件，安装后，可以调用它来运行测试代码。不足之处是不能与junit进行良好的集成，所以对于我做topcoder帮助不大。
<p>但是，这毕竟是一个激动人心的工具，以后mock库的发展趋势，将会是这个方向。
<p>主页：[https://jeasytest.dev.java.net/](https://jeasytest.dev.java.net/)
