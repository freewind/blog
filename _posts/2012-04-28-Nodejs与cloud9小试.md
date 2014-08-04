---
layout: post
title: Node.js与cloud9小试
tags:
  - JavaScript
date: 2012-04-28 19:22:33
---

Node.js使用了google提供的性能强劲的v8引擎，让我们真正可以使用js在服务器端编程。使用它写简单的web程序异常简单，而且它特有的基于事件回调的处理方式，拥有很高的吞吐量。

推荐一本叫《Node.js入门》的电子书，由国人翻译，质量很高，是极好的入门材料。其使用的代码位于：[https://github.com/ManuelKiessling/NodeBeginnerBook/tree/master/code/application](https://github.com/ManuelKiessling/NodeBeginnerBook/tree/master/code/application "https://github.com/ManuelKiessling/NodeBeginnerBook/tree/master/code/application")

我本在win7 x64下，安装nodejs没有问题，但是某些第三方模块不支持windows，所以最好在其它系统如linux下使用node.js。我安装了一个linux的虚拟机，专门用于学习node.js。

这么麻烦？我机器很慢的，装虚拟机跑不动，我不会用linux。。。

好吧，试试cloud9。

当然还有很多其他类似的在线编程环境，直接支持node.js，还有基于浏览器的编程器，还有git，还能直接运行，还免费。这么好的事情，还不去试试？

cloud9的网址是[http://c9.io](http://c9.io)，对于开源项目免费，可支持将github.com和bitbucket.org中的代码库clone过来。直接在线编辑，运行，提交至代码库，非常完美。

上个图看看：

[![image](http://freewind.me/wp-content/uploads/2012/04/image_thumb5.png "image")](http://freewind.me/wp-content/uploads/2012/04/image5.png)

怎么样，像模像样吧？项目管理、语法高亮、debug，甚至还有Deploy可发布到第三方server。使用起来的感觉，跟本地的IDE，几乎没有差别。

点个debug试试：

[![image](http://freewind.me/wp-content/uploads/2012/04/image_thumb6.png "image")](http://freewind.me/wp-content/uploads/2012/04/image6.png)

看到提示信息，Server has started，说明成功启动，访问它提供的链接：[http://testnodejs.freewind.c9.io/](http://testnodejs.freewind.c9.io/ "http://testnodejs.freewind.c9.io/")即可看到如下图：

[![image](http://freewind.me/wp-content/uploads/2012/04/image_thumb7.png "image")](http://freewind.me/wp-content/uploads/2012/04/image7.png)

完美。

这意味着什么？意味着我们的电脑只要能上网，有个浏览器，就能直接编程了！还有什么比这更惬意的事？