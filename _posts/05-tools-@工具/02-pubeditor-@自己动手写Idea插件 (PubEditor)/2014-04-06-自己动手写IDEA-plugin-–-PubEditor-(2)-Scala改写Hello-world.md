---
layout: post
alias: write-idea-plugin-yourself-pubeditor-2-use-scala-to-write-hello-world
tags: Dart
date: 2014-04-06 23:34:36
title: 自己动手写IDEA plugin – PubEditor (2) Scala改写Hello world
---

## 本篇目标

使用Scala改写上次的`HelloWorldAction`，以确认用Scala写插件会不会有什么问题。

## 安装Scala插件

首先需要到[http://www.scala-lang.org](http://www.scala-lang.org)上下载最新的scala安装包，并在IDEA中安装好Scala插件

## 在项目中添加Scala支持

首先打开项目属性，在&#8221;Facets&#8221;中添加Scala：

![QQ20140406-25](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-25.png)

然后在Modules中选择PubEditor的module，添加上scala-library相应的依赖：

![QQ20140406-26](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-26.png)

如果你对Idea下的scala配置不熟悉，需要多一些时间摸索，我也是连蒙带猜弄好的。

## 用Scala重写

删除原来的`HelloWorldAction.java`，新建一个`HelloWorldAction.scala`，内容如下：

    package com.thoughtworks.pli.pub_editor

    import com.intellij.openapi.actionSystem.{CommonDataKeys, AnAction, AnActionEvent}
    import com.intellij.openapi.project.Project
    import com.intellij.openapi.ui.Messages

    class HelloWorldAction extends AnAction {

      def actionPerformed(event: AnActionEvent) {
        val project = event.getData(CommonDataKeys.PROJECT)
        val userName = askForName(project)
        sayHello(project, userName)
      }

      private def askForName(project: Project) = {
        Messages.showInputDialog(project, "What is your name?", "Input Your Name", Messages.getQuestionIcon)
      }

      private def sayHello(project: Project, userName: String) {
        Messages.showMessageDialog(project,
          s"Hello, $userName!\n Welcome to PubEditor.", "Information",
          Messages.getInformationIcon)
      }

    }
    

    可以看到代码跟以前很相似，有一个重要的不同是这一行：

    val project = event.getData(CommonDataKeys.PROJECT)
    

    以前的Java版是这样的：

    Project project = event.getData(PlatformDataKeys.PROJECT);

其中`PROJECT`的确是在`CommonDataKeys`类中声明的，而`PlatformDataKeys`继承了`CommonDataKeys`。在Java中可以在子类中访问父类中的`static`属性，而Scala中就不行。

## 运行

运行方式跟上一篇一样，一切正常。通过这个尝试，发现的确可以用scala代替Java来写Idea插件。

本文所对应的tag为`2_hello_world_scala`，源代码地址在：

https://github.com/freewind/PubEditor

下一篇我们将把'.pub'文件与我们的插件关联起来。
