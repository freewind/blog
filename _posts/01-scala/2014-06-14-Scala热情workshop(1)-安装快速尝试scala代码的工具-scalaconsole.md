---
layout: post
title: "Scala热情workshop(1). 安装快速尝试scala代码的工具: scalaconsole"
tags:
  - scala-hot-workshop
date: 2014-06-14 11:32:36
---

## Scala Console

为了快速尝试一段代码，我们有scala repl, eclipse, idea可用，但是在某些情况下，它们都不够好用。

而scala console这个工具，为scala-repl提供了图形界面，代码高亮等支持，十分方便，实乃居家必备。

### 快速运行代码

上个主界面图：

[![2638699](http://freewind.me/wp-content/uploads/2014/06/2638699.png)](http://freewind.me/wp-content/uploads/2014/06/2638699.png)

左边是编辑区，可以贴scala代码，它提供了高亮、行号等。按”Cmd+R”后，将会在右边内嵌的一个scala-repl中显示结果。

### 快速添加依赖

这是一个非常有用的功能。有时候我们尝试一些库的功能，那会相当麻烦，通常我会这么做：

写一个build.sbt文件，在里面写上所有的依赖，然后`sbt idea`生成，再打开idea写代码。

但是有了scalaconsole，就很简单了：点击菜单中的Dependencies选项，将会出现如下界面

[![487403](http://freewind.me/wp-content/uploads/2014/06/487403.png)](http://freewind.me/wp-content/uploads/2014/06/487403.png)

我们输入关键字，双击右边列表中某个版本，它就会加到下面的列表中。点击OK后，它就会开始下载![9596797](http://freewind.me/wp-content/uploads/2014/06/9596797.png)

期间有半分钟左右没有输出变化，需要耐心等一下。完成后我们就可以直接用了：

[![1042034](http://freewind.me/wp-content/uploads/2014/06/1042034.png)](http://freewind.me/wp-content/uploads/2014/06/1042034.png)

### 发布到gist

如果想与其它人讨论或者分享你的代码，可以点击菜单中的Post Anonymous Gist或者Post Gist with Account，快速发布。

## 项目信息

项目作者：老猪，”无水scala群（231809997）“的群主，是一件严肃而热情的技术高手。公司网站 [http://gtan.com](http://gtan.com)

项目地址：[http://git.oschina.net/43284683/scalaconsole](http://git.oschina.net/43284683/scalaconsole)

### 下载安装

下载地址：[http://git.oschina.net/43284683/scalaconsole/attach_files](http://git.oschina.net/43284683/scalaconsole/attach_files)

ScalaConsole-assembly-2.0.0-M5.jar，大约25M左右

这个文件已经打包了所有需要的文件，你只需要运行：`java -jar ScalaConsole-assembly-$VERSION.jar`即可

注意：由于scalaconsole使用了最新版的JavaFX，你需要安装jdk1.8才能正常运行。

### 启动脚本

你可以手动建立一个scalaconsole.sh，内容如下：

    /path/to/jdk1.8/java -jar ~/dev/scala/ScalaConsole-assembly-2.0.0-M2.jar

然后把它加入到path中，就可以快速启动scalconsole了