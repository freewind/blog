---
layout: post
id: 2717
alias: scala-workshop-2-why-learn-scala-and-difficulties
tags: scala-hot-workshop
date: 2014-06-14 23:34:06
title: Scala热情workshop 2. Scala学习路线(2) 为什么学以及难度分块
---

<div class="se-section-delimiter"></div>
<div class="se-section-delimiter"></div>

## 为什么要学Scala

有不少人问过我这个问题：你为什么要学习Scala?

在最开始，我也是被各种宣传忽悠了，以为Scala是一门简单的，对Java有很多改进的，甚至可能很快就成为下一代Java的语言。当时正打算做一个网站，于是就用它边学边做。几个月后，实在无法忍受它的编译速度、各种类库的缺失、以及各种各样的编译错误，放弃了它。

但是当时创建的那个Scala群里，却有非常好的交流氛围。很多人由于对Scala很有兴趣，在群里讨论各种新鲜好玩的技术，让我大开眼界，发现Java原来只是一个小世界。另外在群里认识了杨云，也因此加入了TW，现在他是我的sponsor。

来到我们公司之后，居然有两次参与Scala项目的机会。发现我们的客户居然都喜欢用Scala，西安办公室里也有越来越多的人主动或者被迫学习Scala。由于现在有了更多学习的时间，所以我又把它捡了起来，同时惊喜地发现之前一直没搞懂的问题，现在竟然差不多理解了。

我认为我现在学习Scala的原因是：它为我打开了编程世界的一扇门，让我看到了与之前完全不同的世界。通过对它的学习，我可以强迫自己学习更多编程知识，提高自己的能力，从而有机会跟更多牛人交流。另外，我个人也看好Scala的未来，所以认为越早掌握它对自己越有利。

## Scala学习路线

结合我自己的学习经历，我把Scala的学习按难度分成了几块。每一块的难度侧重点相对独立，需要一段时间的专门学习。

### 第一块：语法糖

第一块是学习Scala的各种基本特性，比如object, trait, pattern matching等，这些知识对于一个熟练的Java程序员来说，没有太大难度。看完一本Scala书，或者参与一个不太复杂的Scala项目，基本上就可以到达。

推荐书籍： << Scala编程>> 或者 <<快学Scala>>

文章：

1.  Daily Scala: [http://daily-scala.blogspot.com/](http://daily-scala.blogspot.com/)
2.  The Neophyte's Guide to Scala: [http://danielwestheide.com/scala/neophytes.html](http://danielwestheide.com/scala/neophytes.html)

开源项目：[https://github.com/inca/circumflex/tree/master/web](https://github.com/inca/circumflex/tree/master/web)

Circum-web是一个比较简单的scala mvc框架，代码简单、注释丰富。由于它没有用到多少函数式风格的代码，对于初学者来说，还是比较容易上手的。虽然现在用它的人不多，但不失为一个很好的学习资源。同时它还有circum-orm等项目，也可以用来学习。

<div class="se-section-delimiter"></div>

### 第二块：类型系统

此时最困扰我们的，应该就是各种各样类型相关的编译错误，以及一些复杂难懂的类型声明。

特别是这几个方面：

1.  路径依赖类型
2.  非变，协变，逆变
3.  Type class
4.  高阶类型等

推荐书籍：`<<Scala in Depth>>`

博客文章：

1.  [http://hongjiang.info/scala/](http://hongjiang.info/scala/) 中有关类型系统的文章
2.  [http://apocalisp.wordpress.com/2010/06/08/type-level-programming-in-scala/](http://apocalisp.wordpress.com/2010/06/08/type-level-programming-in-scala/)

参考语言：Haskell中的类型系统

<div class="se-section-delimiter"></div>

### 第三块：函数式

这一块的目标就是搞懂monad，以及各种函数式编程中提到的概念。这里可能要结合其它函数式语言学习。

推荐书籍：`<<Scala in Depth>>` `<<functional programming in scala>>`

推荐博客：

1.  [http://hongjiang.info/scala/](http://hongjiang.info/scala/) 中关于monad的文章
2.  Manods are elephants系列: ​[http://james-iry.blogspot.com/2007/09/monads-are-elephants-part-1.html](http://james-iry.blogspot.com/2007/09/monads-are-elephants-part-1.html)
3.  论面向组合子程序设计方法: [http://www.blogjava.net/ajoo/category/6968.html](http://www.blogjava.net/ajoo/category/6968.html)
4.  Learning scalaz: [http://eed3si9n.com/learning-scalaz/](http://eed3si9n.com/learning-scalaz/)

推荐库：scalaz

推荐语言：Haskell

<div class="se-section-delimiter"></div>

### 第四块：生态

前三块基本上都是语言层面，这一块是库，比如一些我们经常用到的，或者scala中一些很有名的库：

构建工具: sbt

1.  scalatest/specs2
2.  scalaz
3.  akka
4.  spark

这里要根据项目和兴趣进行选择。

<div class="se-section-delimiter"></div>

### 第五块：其它

Scala中的一些其它特性，比如:

1.  Dynamic: [http://stackoverflow.com/questions/15799811/how-does-type-dynamic-work-and-how-to-use-it](http://stackoverflow.com/questions/15799811/how-does-type-dynamic-work-and-how-to-use-it)
2.  macro
3.  ​scala.js, jsscala
