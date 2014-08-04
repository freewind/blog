---
layout: post
title: "自己动手写IDEA plugin – PubEditor (1) Hello, world"
tags:
  - Dart
date: 2014-04-06 23:06:06
---

我发现在实现编辑器功能时，比较容易用到编译原理方面的东西，比如高亮、格式化、代码提示、重构等，所以我打算通过开发一些编辑器插件来锻练。而IDEA是我们用的最多，也是我很喜欢的一个编辑器，所以就有了“自己动手写IDEA plugin”系列。

## 任务目标

这个任务的目标有：

1.  实现一个功能完善的IDEA plugin。包括格式化，高亮，代码提示等功能
2.  全面支持dart的pub。[pub](http://pub.dartlang.org)是dart的依赖管理工具，可定义、下载、管理dart包依赖
3.  使用scala来实现

基本上把我目前关注的几个方面都包括了。

## 本篇目标

作为开篇，我们将实现最基本的功能：创建一个最最基本的idea plugin，可以弹出一个Hello World的提示。

为了方便，这里只使用Java实现，下一篇会改成scala的

## 准备工作

### 入门教程

首先是IDEA plugin开发入门，非常值得一看，了解一下背景知识。

http://confluence.jetbrains.com/display/IDEADEV/PluginDevelopment

但是其中关于[如何建立项目](http://confluence.jetbrains.com/display/IDEADEV/Getting+Started+with+Plugin+Development#GettingStartedwithPluginDevelopment-anchor2)的一节讲得太繁琐，根本看不懂。不过好在没不是很复杂，跟着本文走就行了。

另外，上面列出的内容比较多，我没有全部看完，只看了前几篇，足够把本篇的任务完成。

### 下载IDEA

如果临时试一下，下载IDEA的社区版或企业版都行，也不需要下载源代码。但如果深入开发，就只能使用社区版，因为它可以跟源代码结合起来，方便调试。

下载IDEA: [http://www.jetbrains.com/idea/download/](http://www.jetbrains.com/idea/download/)

随便下一个就行，不过为了方便，推荐社区版。

### 下载source

不论是社区版还是企业版，里面都自带插件开发工具，可以直接用。但是为了以后调试的方便，还需要下载idea的源代码，将它们关联起来，就可以单步了。

使用git下载社区版对应的源代码：

    git clone git://git.jetbrains.org/idea/community.git idea --depth 1
    

    这是一个非常大的库，就算添加了`--depth 1`的参数，下载的源代码打成.tar.gz包后也超过了1G。由于代码库在国外，对于我们大部分人来说，这不是一天两天能完成的任务。

    这几天访问国外网络非常不稳定，所以我只好在一个国外的vpn上下载完代码，然后打包传过来。gz包的大小为1.18G，还要传19个小时。。。

    ### 启用Plugin DevKit插件

    Idea中默认自带了`Plugin DevKit`插件，但是没有开启。首先我们需要将它开启:

    [![QQ20140406-16](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-16.png)](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-16.png)

    然后激动人心的时刻就要开始了！

    ## 创建项目

    打开Idea，创建一个新项目，记得要选左右要选&#8221;Intellij Plugin Platform&#8221;类型：

    ![QQ20140406-4](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-4.png)

    然后输入项目名，我选的是`PubEditor`:

    ![QQ20140406-5](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-5.png)

    创建完成后，就自动打开项目，注意在`META-INF`下有一个`plugin.xml`文件：

    ![QQ20140406-6](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-6.png)

    这个plugin.xml文件是一个重要的配置文件，用于向IDEA平台注册我们的提供的功能。如果你开发过eclipse插件，会知道那边也有一个类似的文件。

    ## 添加Action

    我打算实现一个功能：在菜单最后添加一项&#8221;Pub Editor&#8221;，里面有一个&#8221;Hello world&#8221;的子菜单。当点击它以后，会提示一个对话框，让我们输入自己的名字，然后弹出欢迎的信息。

    首先我们需要实现一个`AnAction`的子类，用于实现触发之后的功能：

    package com.thoughtworks.pli.pub_editor;

    import com.intellij.openapi.actionSystem.AnAction;
    import com.intellij.openapi.actionSystem.AnActionEvent;
    import com.intellij.openapi.actionSystem.PlatformDataKeys;
    import com.intellij.openapi.project.Project;
    import com.intellij.openapi.ui.Messages;

    public class HelloWorldAction extends AnAction {

        @Override
        public void actionPerformed(AnActionEvent event) {
            Project project = event.getData(PlatformDataKeys.PROJECT);
            String userName = askForName(project);
            sayHello(project, userName);
        }

        private String askForName(Project project) {
            return Messages.showInputDialog(project,
                    "What is your name?", "Input Your Name",
                    Messages.getQuestionIcon());
        }

        private void sayHello(Project project, String userName) {
            Messages.showMessageDialog(project,
                    String.format("Hello, %s!\n Welcome to PubEditor.", userName), "Information",
                    Messages.getInformationIcon());
        }
    }
    

    可以看到，它实现了父类`AnAction`中的`actionPerformed`方法，提供了自己的逻辑。一个是弹出一个`inputDialog`让用户输入自己的名字，另一个是弹出一个`messageDialog`显示欢迎信息。

    ## 在plugin.xml中注册

    只提供上面的类还不够，还需要在plugin.xml中添加一些注册信息。

    打开plugin.xml，找到其中的`<actions>`一节，添加：

    <group id="PubEditorPlugin.Menu" text="_Pub Editor" description="Pub Editor Menu">
        <add-to-group group-id="MainMenu" anchor="last"/>
        <action id="PubEditorPlugin.HelloWorldAction" 
                class="com.thoughtworks.pli.pub_editor.HelloWorldAction"
                text="_Hello world" description="Hello world from PubEditor"/>
    </group>
    

    这是一段信息量很大的配置代码。外面的`group`指定了菜单组的位置`last`，显示的内容为`Pub Editor`，它前面的`_`表示可用快捷键`p`访问。里面的`action`指定了菜单项显示的内容为`Hello world`，对应的类为我们刚创建的`HelloWorldAction`类。

    ## 代码部分结束

    代码部分就结束了，东西不多，过程还是很轻松的。我的项目文件是这样的：

    ![QQ20140406-17](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-17.png)

    下面是运行效果了。

    ## 运行插件

    不能直接运行，而要先建立一个配置。打开菜单&#8221;Run&#8221;->&#8221;Configuration&#8221;，点击左上角的&#8221;+&#8221;新建，选择&#8221;Plugin&#8221;，如图：

    ![QQ20140406-18](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-18.png)

    基本上不用改什么，我就只把名字设成了&#8221;Pub Editor&#8221;，然后点击&#8221;OK&#8221;.

    这时右上角运行栏处会变成这样：

    ![QQ20140406-19](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-19.png)

    点击绿色小三角即可。

    它会打开一个新的IDEA。如果你运行的是企业版，可能会提示你输入license等信息，可以选择“继续试用”，进入开始界面：

    ![QQ20140406-20](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-20.png)

    由于我们添加的菜单只能在进入项目后才能看到，所以我们需要随便创建一个项目。我建的是&#8221;TestPlugin&#8221;，进去后可以看到上面的菜单最右边多了一项&#8221;Pub Editor&#8221;，以及&#8221;Hello world&#8221;:

    ![QQ20140406-22](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-22.png)

    点击&#8221;Hello world&#8221;后，会弹出对话框，提示我们输入名称：

    ![QQ20140406-23](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-23.png)

    输入名字后，会弹出一个新的信息框：

    ![QQ20140406-24](http://freewind.me/wp-content/uploads/2014/04/QQ20140406-24.png)

    ## 源代码

    作为Hello world，本文到此就结束了。源代码已经上传至:

    http://github.com/freewind/PubEditor

本文所对应的tag为&#8221;1_hello_world&#8221;，你可以checkout它运行一下看结果。