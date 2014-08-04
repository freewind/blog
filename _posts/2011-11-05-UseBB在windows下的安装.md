---
layout: post
title: UseBB在windows下的安装
tags:
  - Scala
date: 2011-11-05 21:45:37
---

我接受了林教主的委托，现在开始尝试使用liftweb+squeryl+mysql来实现一个scala版的usebb。教主要求其做的与原版usebb一模一样，重点考察squerl的使用，填上技术上的坑。

我的scala一直半生不熟，这也算是一个激励我重学scala的机会。我个人对liftweb不太感冒，总觉得它有点复杂，比不上play2的简洁。可惜play2现在远未成熟，liftweb还是最为稳妥的选择。另外，群里几位老大都很推荐liftweb，说它做网站代码很简洁，我也想尝试一下，看看跟play的开发效率相比谁高谁低。再就是squeryl了，在scala的世界了，它可能算是比较知名的orm，我曾经简单试用过，没达到期望。也好，趁此机会好好用用，说不定有意外惊喜。

<span id="more-548"></span>
<p>开工第一件事，下载安装usebb.net。

网址：[http://usebb.net](http://usebb.net)。我下载了zip包，在windows上安装。

我使用一个叫APMServ5.2.6的php集成环境，十分方便且免费。它把php、apache、mysql什么的，都弄在一起了，启动时只需要运行它提供的一个GUI程序即可。

安装过程很简单：

1.  把usebb解压缩，放在APMServ5.2.6\www\htdocs\ubb目录下
2.  直接以文件方式打开ubb/docs/index.html，它是安装教程，可参考
3.  将ubb/config.php-dist改名为config.php
4.  在浏览器中打开[http://localhost/ubb/install/install.php](http://localhost/ubb/install/install.php)开始安装

这时发现500错误。且不论打开任何php文件或者html文件，都是这个错误，真奇怪。

一点点排查，发现在ubb目录下有一个.htaccess文件，里面定义了很多url rewrite的规则。可能是APMServ中的配置不对，导致rewrite无法正常工作而出错。最终解决办法是删除（或改名）这个.htaccess，就可以看到正常的安装页面了。

我需要先在mysql中新建一个数据库，然后在安装页面上填写相关的配置信息，如数据库名，用户，密码等。需要注意的是，管理员的密码必须是字母与数字混合，并且长度在6位以上，不然总提示错误（错误信息不太明显，看了半天才发现）。

所有信息无误的话，点击下一步，就安装好了。这时会要求我们要把install目录删除或改名，否则打开首页时会提示安全问题，不能继续运行。

再次打开首页[http://localhost/ubb](http://localhost/ubb)，看到了论坛的首页，一切正常。之前删除了.htaccess，所以url看起来不太好看，不过不影响正常使用。

截个图，如下：

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb.png "image")](http://freewind.me/wp-content/uploads/2011/11/image.png)