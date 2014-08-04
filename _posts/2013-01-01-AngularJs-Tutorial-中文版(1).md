---
layout: post
title: AngularJs Tutorial 中文版(1)
tags:
  - JavaScript
date: 2013-01-01 11:59:52
---

学习AngularJS的一个好办法，就是在这份简明教程的帮助下，动手实现一个简单的AngularJS web程序。该程序以分类方式来显示一些Andorid设备，你可以筛选出感兴趣的设备，并查看某个设备的详细资料。

[![image](http://freewind.me/wp-content/uploads/2013/01/image_thumb.png "image")](http://freewind.me/wp-content/uploads/2013/01/image.png)

 

Angular可以让你的浏览器更好更强大（不需要用浏览器插件或者扩展程序）。当你阅读这份教程时，你将会：

1.  看到示例演示如何使用client-side的数据绑定和依赖注入来创建动态的数据视图，当用户进行操作后，相应数据视图会立刻跟着变化
2.  看到Angular如何监听数据的变化，但不需要操作DOM
3.  学习到一种更好更简单的方法来测试你的web程序
4.  学习如何使用Angular提供的服务，来更轻松地实现常见的web任务，如把数据嵌入到程序中

它们可以在任何浏览器中工作，不需要对浏览器做任何改变！

当你完成了这份“简明教程”后，你可以做到：

1.  建立一个可以运行于任意浏览器中的动态程序
2.  了解AngularJs与其它JavaScript框架之间的区别
3.  理解AngularJs中的数据绑定是如何工作的
4.  使用angular-seed项目快速搭建你自己的项目
5.  编写并运行测试
6.  自行找到更多的资源来进一步学习AngularJS

该教程将带领你走完创建一个简单web程序的全过程，甚至包括如何编写和运行end-to-end的测试。每一步最后的实验将向你提供一些建议，帮助你学习更多AngularJS的知识，和理解你正在创建的程序。

你只需要花几个小时就能看完这整份文档，或者花一天时间来享受深入研究它的乐趣。如果你在寻找一份更简短的AngularJS介绍，请看新手指南。

## 示例代码在哪儿

在你阅读这份教程的时候，你可以在Mac/Linux/Windows上，亲手编写运行示例代码。你可以使用Git版本控制工具从github上来获取和管理示例，或者用一个脚本把最新的项目文件复制到你的工具目录中。

从下面的几种方式中，选择一种适合你的方式，先把工作环境搭好。

## 在Mac/Linux上使用Git

1. 检查你电脑上是否安装了java。在命令行中输入以下命令：

<div class="mycode">java -version</div>

Java用来跑单元测试。

2. 从Git官网上下载Git

你可以从Git源代码编译，或者使用预编译的包。

3. 使用以下命令，从github上clone我们的示例项目angular-phonecat的仓库到本机：

git clone git://github.com/angular/angular-phonecat.git   
  
该命令将在你当前目录下建立一个angular-phonecat目录，并把文件放置其中。

4. 进入angular-phonecat目录:

cd angular-phonecat   
  
在本教程中，都假设你的命令运行于angular-phonecat目录下。

5. 你需要在本机运行一个http服务器。 Mac和Linux通常都预装了apahce。如果你的机子上没有，你也可以安装node.js. 使用node来运行angular-phonecat目录下的scripts/web-server.js文件即可，它是一个简单的http server。

## 在Windows上使用Git

1. 你需要使用java来运行单元测试，所以运行以下命令来检查你的电脑上是否安装了java，并且它位于环境变量的PATH中。

<div class="mycode">java -version</div>
<p>   
如果没有，请到java官网上下载安装。

2. 从Git官网下载并安装msysGit

3. 打开msysGit的命令行工具并从Github上clone下angular-phonecat仓库。使用以下命令：

<div class="mycode">git clone git://github.com/angular/angular-phonecat.git</div>

该命令将在你当前目录下建立一个angular-phonecat目录，并把文件放置其中。

4. 进入angular-phonecat目录:

<div class="mycode">cd angular-phonecat</div>
<p>   
在本教程中，都假设你的命令运行于angular-phonecat目录下。

你应该在msysGit的命令行工具中，运行所有的git命令。

而其它的命令（比如test-server.bat或test.bat）可以直接在windows的命令行中执行。

5. 你的系统上需要运行一个http服务器。如果没有的话，可以下载node.js。确保nodejs\bin已经加入到环境变量的PATH中。然后使用node来运行angular-phonecat下的scripts\web-server.js，来启动一个简单的内置服务器。

## 在Mac/Linux上使用Snapshots

1. 检查你电脑上是否安装了java。在命令行中输入以下命令：

<div class="mycode">java -version</div>

Java用来跑单元测试。

2. 下载包含了最新文件的zip包，并把它解压到某目录下（假设为tutorial-dir）

3. 进入tutorial-dir/sandbox目录:

cd tutorial-dir/sandbox

在本教程中，都假设你的命令运行于sandbox目录下。

4. 你需要在本机运行一个http服务器。 Mac和Linux通常都预装了apahce。如果你的机子上没有，你也可以安装node.js. 使用node来运行angular-phonecat目录下的scripts/web-server.js文件即可，它是一个简单的http server。

## 在Windows上使用Snapshots

1. 你需要使用java来运行单元测试，所以运行以下命令来检查你的电脑上是否安装了java，并且它位于环境变量的PATH中。

java -version   
  
如果没有，请到java官网上下载安装。

2. 下载包含了最新文件的zip包，并把它解压到某目录下（假设为tutorial-dir）

3. 进入tutorial-dir/sandbox目录:

cd tutorial-dir/sandbox   
  
在本教程中，都假设你的命令运行于sandbox目录下。

4. 你的系统上需要运行一个http服务器。如果没有的话，可以下载node.js。确保nodejs\bin已经加入到环境变量的PATH中。然后使用node来运行angular-phonecat下的scripts\web-server.js，来启动一个简单的内置服务器。

最后需要确定的是，你的电脑上要有一个浏览器和一个编辑器。

现在，让我们开始做一些很酷的事情吧！