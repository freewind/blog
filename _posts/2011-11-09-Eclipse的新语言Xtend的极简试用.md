---
layout: post
title: Eclipse的新语言Xtend的极简试用
tags:
  - Java
date: 2011-11-09 22:12:54
---

一直盼着Idea的新语言kotlin，久久没有动静，没想到它的对手Eclipse居然突然发布了一个新语言，叫Xtend

网址在：[http://www.eclipse.org/Xtext/xtend/](http://www.eclipse.org/Xtext/xtend/)

BUG提交地址：[https://bugs.eclipse.org/bugs/enter_bug.cgi?product=TMF&amp;component=Xtext&amp;version=2.1.0&amp;short_desc=%5BXtend%5D](https://bugs.eclipse.org/bugs/enter_bug.cgi?product=TMF&amp;component=Xtext&amp;version=2.1.0&amp;short_desc=%5BXtend%5D)

安装时需要通过Eclpise的Market，直接搜索xtend就能找到。

Xtend是在Xtext的基础上发展出来的。这个Xtext，是Eclipse提供的一种可用来设计DSL的工具，卖点是Eclipse对它提供了非常好的编辑器支持，如语法高亮、自动提示、重构等等，用它设计出来的DSL在eclipse中看起来就像是一种新语言一样，比较有新引力。我当时在想，不知道能不能用它来设计一些复杂的语言，像Scala之类。没想到几个月过去了，居然抛出了一个Xtend的语言。

Xtend是对Java的一些小小改进，提供了一些Javaer一直想要却迟迟不来的，比如多行文本、文本内嵌表达式、闭包等等。Xtend的编译器将把Xtend编译为可读性很好的Java源代码，注意，是源代码！而不是如scala, groovy那样生成字节码。它相对于Java，就如同coffeescript相对于javascript。

这对于我来说，是很有吸引力的。因为我将scala与java一起使用时，有时候调用java并不方便，并且还必须使用scala自己的编译器，IDE的支持也不太好。而Xtend直接被编译为java源代码，它就相当于一个智能的代码生成器，有时候很方便：比如在play中，我直接写xtend，eclipse会把它编译为Java。由于Play还是可以看到Java代码，所以不需要担心classloader之类的问题，可直接使用。

说到这里，其实我在play中，早已经使用了类似的生成器，比如写haml由工具生成html，写less由工具生成css，这次如果能写xtend生成java，那真是全了。

<span id="more-571"></span>
<p>怀着激动的心情与漫长的安装，终于把xtend装好了，新建了一个内置的xtend教学项目，看看代码的截图：

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb6.png "image")](http://freewind.me/wp-content/uploads/2011/11/image6.png)

看起来不错，很有Java的感觉。语法像、配色像，但有一些增强的功能，比如图中的多行文本以及字符串中的内嵌表达式，很爽吧！再来个闭包：

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb7.png "image")](http://freewind.me/wp-content/uploads/2011/11/image7.png)

还没看来及细看，马上发现了问题：太卡了！太卡了！太卡了！太卡了！太卡了！太卡了！

稍稍修改一下代码，保存一下，就会出现如下等待框：

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb8.png "image")](http://freewind.me/wp-content/uploads/2011/11/image8.png)

如果你随便改点东西，然后快速按两次保存，这个对话会卡住超过10秒钟！在此期间什么也不能操作，只能干等！

我的电脑是三年前买的，配置为：AMD Athlon X2 4000+，内存4G，操作系统windows xp。运行Java还是比较流畅的，但跑不起xtend。

为了Xtend（也为了Scala），终于决定升级电脑了。花了1000块钱，现在的配置如下：AMD X3 445，内存8G。由于Windows xp是32位系统，无法给eclipse分配大内存（例如scala插件要求1.5G，但最多只能分750M），于是安装了64位的windows 7，64位的Java和Eclipse。经过一天的折腾，效果很明显：现在运行xtend基本上很流畅，只是在极少数情况会出现一个一闪而过的等待窗，感觉好多了。

我想使用xtend来写play代码，很快发现了一个严重问题：xtend目前不支持static方法！由于play中，所有的action都是static的，这意味着无法在play中用xtend。好在官方消息，会在近期增加static支持（大约一个月以后），那只能再等等了。

尽管如此，还是期待它能好好发展，到时候左手Xtend，右手Kotlin，胸前Scala，威武！