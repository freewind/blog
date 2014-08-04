---
layout: post
title: 自己动手写IDEA plugin – PubEditor (3) 将pub文件与PubEditor关联起来
tags:
  - Dart
date: 2014-04-07 18:31:13
---

Dart使用Pub进行依赖管理。我们需要在项目目录下创建一个名为`pubspec.yaml`的文件，供Pub使用。

## 本篇目标

我们的PubEditor要能够识别`pubspec.yaml`文件。当打开该文件时，会调用PubEditor进行编辑。

注：我们暂不考虑`pubspec.yaml`的内容，仅关注文件名即可。

## 必读资料

下面两篇文章非常重要，基本上提供了我们目前所需要的所有功能。

*   [Developing Custom Language Plugins for IntelliJ IDEA](http://confluence.jetbrains.com/display/IDEADEV/Developing+Custom+Language+Plugins+for+IntelliJ+IDEA)
*   [Language and File Type](http://confluence.jetbrains.com/display/IntelliJIDEA/Language+and+File+Type)

## 文件图标

当我们将某种类型的文件识别为一种语言时，Idea可以让我们为它提供一个自定义的图标。这个图标将会显示在文件树中文件名节点的左边，以及编辑器标题的左边。

文件大小为16&#215;16，格式为png。

我使用一个[在线版photoshop](http://www.uupoop.com/)，生成了下面这个图标:

![pub](http://freewind.me/wp-content/uploads/2014/04/pub.png)

取名为`pub_file.png`

## 自定义Language

这个PubEditor实际上就是一个小型的自定义语言插件。对于这类插件，我们需要定义一个Language子类：

    package com.thoughtworks.pli.pub_editor

    import com.intellij.lang.Language

    class PubLanguage extends Language("pub")

    object PubLanguage extends PubLanguage
    

    代码很简单，只需要继承`Language`即可。其中的&#8221;pub&#8221;参数是照着教程来的，不太清楚到底有什么用（Idea源代码还没下完，看不了），不过写成&#8221;pub&#8221;应该没什么问题。

    `object PubLanguage`是一个单例，方便在其它类中直接引用。

    ## 自定义FileType

    我们还需要自定义一个LanguageFileType，提供名称、描述和图标：

    package com.thoughtworks.pli.pub_editor

    import com.intellij.openapi.fileTypes.LanguageFileType
    import org.jetbrains.annotations.NotNull
    import org.jetbrains.annotations.Nullable
    import javax.swing.Icon
    import com.intellij.openapi.util.IconLoader

    class PubFileType extends LanguageFileType(PubLanguage) {

      @NotNull
      override def getName = "Pub"

      @NotNull
      override def getDescription = "Dart's Pub File"

      @NotNull
      override def getDefaultExtension = ""

      @Nullable
      override def getIcon: Icon = IconLoader.getIcon("/com/thoughtworks/pli/pub_editor/pub_file.png")

    }

    object PubFileType extends PubFileType
    

    其中的`getDescription`，会在关联某种文件时，显示在列表中：

    ![QQ20140407-27](http://freewind.me/wp-content/uploads/2014/04/QQ20140407-27.png)

    而`getDefaultExtension`是说默认把哪种后缀名的文件与该插件关联起来。由于我们需要对`pubspec.yaml`这个完整的文件名，而不是某个后缀名进行关联，所以我只好把它留空。

    `getIcon`会让我们提供一个自定义的图标供使用：

    ![QQ20140407-28](http://freewind.me/wp-content/uploads/2014/04/QQ20140407-28.png)

    而`getName`我也不知道有什么用，可能是用来标识这种类型的文件，就叫`Pub`吧。

    ## 自定义FileTypeFactory

    我们还需要提供一个`FileTypeFactory`子类，用来把我们的定义的`FileType`与某种类型的文件关联起来。同时还要把它注册到插件配置文件里。

    package com.thoughtworks.pli.pub_editor

    import com.intellij.openapi.fileTypes.{FileNameMatcher, FileTypeConsumer, FileTypeFactory}
    import org.jetbrains.annotations.NotNull

    class PubFileTypeFactory extends FileTypeFactory {

      val PubFileName = "pubspec.yaml"

      @Override
      override def createFileTypes(@NotNull fileTypeConsumer: FileTypeConsumer) = {
        fileTypeConsumer.consume(PubFileType, new FileNameMatcher {
          override def accept(fileName: String) = fileName == PubFileName
          override def getPresentableString: String = PubFileName
        })
      }
    }
    

    可以看到只有当文件名为`pubspec.yaml`时，才会把它与PubEditor关联起来。

    然后在`plugin.xml`中注册：

    <extensions defaultExtensionNs="com.intellij">
        <fileTypeFactory implementation="com.thoughtworks.pli.pub_editor.PubFileTypeFactory"/>
    </extensions>
    

    ## 运行

    运行效果如下图。当我创建了一个`pubspec.yaml`文件时，IDEA立刻识别出来，并显示了我们自定义的图标。而创建了其它的文件，如`abc.yaml`，则关联上了其它的编辑器：

    ![QQ20140407-29](http://freewind.me/wp-content/uploads/2014/04/QQ20140407-29.png)

    ## 源代码

    http://github.com/freewind/PubEditor

切换到`3_associate_pubspec_yaml`标签即可。

本篇关联的编辑器是默认的文本编辑器，功能有限。而`pubspec.yaml`使用了`yaml`格式，下一篇将让我们的编辑器基于yaml插件。