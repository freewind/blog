---
layout: post
id: 2065
alias: how-to-integration-livereload-in-playframework1
tags: PlayFramework1
date: 2013-02-03 18:18:42
title: 如何在playframework中集成livereload
---

Livereload是一个非常贴心的功能，可以让我们修改项目中的文件时，浏览器自动刷新取得新代码，省了手动切换刷新。在[我的web开发最强组合：Play1+angularjs+bootstrap ++ (idea + livereload)](http://freewind.me/blog/20121226/1167.html)一文中，有gif动画来演示效果。然而稍嫌麻烦的是，我们需要安装python/easy_install/python-livereload等工具，并且每次使用时都得开一个shell，定位到工作目录，输入`livereload`命令才行。

如果能在playframework中集成livereload功能，每次输入play run时就自动提供livereload服务，就太好了。经过一翻折腾，终于实现。

实现这种功能，有三种可行的方式：

1.  在java代码中调用外部命令（即python库livereload）2.  集成一个java实现的livereload库3.  集成一个python实现的livereload库

三种方式各有利弊，我开始选择的是第2种，并找到一个可用的项目：[https://github.com/davidB/livereload-jvm](https://github.com/davidB/livereload-jvm)

这个项目实现了livereload的协议，我们可以把它拿过来改改，使用play内置的websocket功能替代原有的基于jetty的库。但它有一个很大的问题：为了能高效地知道哪些文件的状态发生了变化（修改、删除），必须使用jdk7中提供的一些类。也就是说，要使用它，我们必须使用jdk7。这是一个比较大的限制。

后来突然想到，play1本身使用python2.6来启动，能不能直接把python-livereload库集成进去?比如，当我们输入play run的时候，让它同时启动livereload功能？经过一翻尝试，终于实现。

## 在play的python中安装livereload库

一般在python中安装第三方库，都是使用如easy_install/pip这些库管理工具，但我们也可以直接通过源代码安装。

在每个python的库中，都可以找到一个setup.py的文件，只需要执行：

    python setup.py install

    即可。

    需要注意的是，由于我们要把库安装到play的python里去，这里要指明python的路径，比如：

    E:\play\python\python.exe setup.py install

    ### setuptools

    首先要安装setuptools这个库，进入其主页[http://pypi.python.org/pypi/setuptools#downloads](http://pypi.python.org/pypi/setuptools#downloads)下载。注意要选用源代码打的包（以.tar.gz）结尾的那个。

    下载后解压，并安装：

    cd setuptools-0.6c11
    E:\play\python\python.exe setup.py install

    安装成功后，它会有提示。

    ### python-livereload

    然后安装python-livereload，到github上下载其源代码：[https://github.com/lepture/python-livereload](https://github.com/lepture/python-livereload)

    通过git clone或者直接下载zip文件的方式下载到本地后，解压并安装：

    cd python-livereload
    E:\play\python\python.exe setup.py install

    它会自动安装python-livereload引用的其它库，如果没有错误，则会提示安装成功。

    这时打开play目录下的python目录，可以看到里面多了一个`Scripts`目录，并且里面有`livereload.exe`等文件。

    ## 修改play脚本

    现在将要修改play的脚本，以调用livereload库。

    找到`play\framework\pym\play\commands\base.py`文件，它定义了play的常用命令，如`play run`，`play clean`等。

    首先添加一个与def run平级的函数livereload，它的代码是我从livereload-script.py中copy过来的：

    def livereload():
        __requires__ = 'livereload==0.11'
        import sys
        from pkg_resources import load_entry_point
        sys.exit(
            load_entry_point('livereload==0.11', 'console_scripts', 'livereload')()
        )

    我们需要在run()函数中调用这个函数，但要注意的是，我们不能直接用它，因为它是阻塞的，一旦运行就会阻塞当前线程，后面的代码就没机会执行了，所以得为它新开个线程。

    将下面的代码添加到def run()函数中，在`java_cmd = app.java_cmd(args)`之前：

    import thread
    thread.start_new_thread(livereload, ())

这样就大功告成了。现在可以在运行play run的同时另起一个线程执行livereload功能，而不需要额外操作。可以直接使用浏览器端的livereload插件进行自动刷新了。

## 效果

[![image](/user_images/2065-1.gif "image")](/user_images/2065-1.gif)

观看时注意“新建”按钮上文字的变化。当我修改了html中的“新建”的内容并保存后后，右上方浏览器中的“新建”会自动变化。
