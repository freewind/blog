---
layout: post
id: 2461
alias: atom-spark
tags: Dart
date: 2014-03-27 19:57:35
title: Atom vs Spark：用Node/Dart+css+html打造编辑器
---

## Atom

感谢好友子叶发过的Atom邀请，我体验了一下可用Javascript/css/html写插件的编辑器。

官网：[http://atom.io/](http://atom.io/)

官网现在还处于非公开状态，需要有邀请才能下载。它是一个核心部分还未开源的编辑器，最大特点是内置了node.js，可以让人们使用Javascript/css/html写插件，即可以利用node.js的功能，又可以利用上成千上万的js库，相当强大。

运行起来很流畅，有点sublime的感觉，所以我怀疑它自己并不是用nodejs写的：

[![QQ20140327-2](http://freewind.me/wp-content/uploads/2014/03/QQ20140327-2.png)](http://freewind.me/wp-content/uploads/2014/03/QQ20140327-2.png)

从官方文档上可以看到其功能已经相当完善，插件生态圈也差不多建起来了，目前已经有600多插件。我挑了一个插件看了一下，里面用到了coffeescript/less等，功能还比较复杂，但结构很清晰。也许因为Javascript给人的轻量感觉，让我觉得写一个插件出来似乎不是一件很困难的事情，不像给Eclipse/Idea写插件那样压力巨大。

Atom目前只支持Mac平台，不久会推出Windows/Linux。另外它是一个商业软件，并不是免费的，但是“会提供与类似编辑器相比很有竞争力的价格”。

## Spark

Node.js平台都有编辑器了，Dart呢？

Dart有一个叫Spark的项目，利用Dart + Polymer在chrome上创建一个编辑器。这个项目还相当早期，只有不多的文档和源代码，需要开发者自己准备好环境、编译、执行才能看到结果。

我经过一翻折腾，终于在今天把它跑起来了，果然是相当的早期！与前面的Atom大哥相比，实在不好意思露脸。。。

[![QQ20140327-1](http://freewind.me/wp-content/uploads/2014/03/QQ20140327-1.png)](http://freewind.me/wp-content/uploads/2014/03/QQ20140327-1.png)

不论是界面还是功能都非常的简陋，我感觉和一个“记事本”差不多，后台还经常报错。只能说Dart很新，Polymer也很新，一切都还在探索之中。

不过我个人对它还是很有兴趣的，至少可以拿来学习。而且，我对Dart还是很有信心的，相信Spark也会越来越好的。
