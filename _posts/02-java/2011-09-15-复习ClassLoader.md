---
layout: post
title: 复习ClassLoader
tags: Java
date: 2011-09-15 15:38:53
---

这两天在尝试把activejdbc集成到play中。因为两者都需要对类进行增强，有冲突，所以不能直接使用。经过一晚上的反复尝试，终于能让activejdbc在play上跑起来了。但是，很快发现，一旦model类被修改，play重新载入后会报错。

源代码为：

<pre class="csharpcode"><font color="#ff0000">**<span class="kwrd">for</span> (User user : users) {
**</font>     System.err.println(<span class="str">">>> "</span> + user); 
}```

红色那行报错为：

> **ClassCastException** occured : models.User cannot be cast to models.User

一个&#8221;models.User&#8221;不能转换为另一个&#8221;models.User&#8221;，只能说明一种情况：这个类被两个不同classloader导入了。

为了完全解决这个问题，我需要好好复习一下Java的classloader机制以及play的classloader运作原理。

<span id="more-238"></span>
<p>**几篇不错的资料**

1.  [http://blog.csdn.net/turkeyzhou/article/details/2776752](http://blog.csdn.net/turkeyzhou/article/details/2776752)
<li>[http://wolfware.bokee.com/3412061.html](http://wolfware.bokee.com/3412061.html)

看完这两个，再随便找点别的看看，差不多就能明白个大概了：

1.  Jvm已经提供了三个级别的ClassLoader，分别是boot -> ext -> app
<li>双亲委托：即在找一个类之前，先交给上级，上级搞不定，再自己动手
<li>全盘负责：如果自己动手，导入一个类时，它所引用的其它类也由自己导入（导入过程中，遵守双亲委托规则）
<li>共享ClassLoader：通过Thread.currentThread().set/getContextClassLoader()
<li>两个classloader导入同一个类，不能互相转换。JVM把它们当作不同的类

**JVM默认提供的ClassLoader**

<pre class="csharpcode"><span class="kwrd">public</span> <span class="kwrd">class</span> Test {
    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> main(String[] args) {
        <span class="rem">// AppClassLoader</span>
        ClassLoader app = Test.<span class="kwrd">class</span>.getClassLoader();
        System.<span class="kwrd">out</span>.println(app);

        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        System.<span class="kwrd">out</span>.println(systemClassLoader);

        <span class="rem">// Ext</span>
        ClassLoader ext = systemClassLoader.getParent();
        System.<span class="kwrd">out</span>.println(ext);

        <span class="rem">// Boot</span>
        ClassLoader boot = ext.getParent();
        System.<span class="kwrd">out</span>.println(boot);

        <span class="rem">// Context</span>
        ClassLoader context = Thread.currentThread().getContextClassLoader();
        System.<span class="kwrd">out</span>.println(context);
    }
}```

打印结果如下：

<pre class="csharpcode">[sun.misc.Launcher$AppClassLoader@addbf1
sun.misc.Launcher$AppClassLoader@addbf1
sun.misc.Launcher$ExtClassLoader@42e816
<span class="kwrd">null</span>
sun.misc.Launcher$AppClassLoader@addbf1](mailto:sun.misc.Launcher$AppClassLoader@addbf1sun.misc.Launcher$AppClassLoader@addbf1sun.misc.Launcher$ExtClassLoader@42e816nullsun.misc.Launcher$AppClassLoader@addbf1)```

从中可以看到，我们通常都是在跟AppClassLoader打交道。
