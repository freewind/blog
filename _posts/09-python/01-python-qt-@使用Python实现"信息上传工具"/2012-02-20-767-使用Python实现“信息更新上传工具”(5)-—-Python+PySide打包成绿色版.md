---
layout: post
id: 767
alias: use-python-to-write-a-tool-to-upload-data-5
tags: Python
date: 2012-02-20 22:59:07
title: 使用Python实现“信息更新上传工具”(5) — Python+PySide打包成绿色版
---

使用PySide写了一个简单的GUI程序，可以在安装了python和QT的环境中正常运行，那么，能否把它打包成绿色版，在没有安装python和qt的环境中运行呢？如果可以，文件一共有多少呢？

经过尝试，终于成功，文件大小为23.2M，列表如下：

> PySide.QtCore.pyd      
> PySide.QtGui.pyd       
> QtCore4.dll       
> QtGui4.dll       
> _hashlib.pyd       
> bz2.pyd       
> library.zip       
> main.exe       
> pyside-python2.7.dll       
> python27.dll       
> select.pyd       
> shiboken-python2.7.dll       
> unicodedata.pyd       
> w9xpopen.exe

里面的main.exe是我写的main.py转换后的文件，其它的，python27.dll是python库，QtGui4.dll和QtCore4.dll是Qt4里的库，PySide.QtCore.pyd和PySide.QtGui.pyd是PySide的库，还有其它的一些，总之，现在可以在一台普通的winxp上运行了。效果如下图：

[![image](/user_images/767-1.png "image")](/user_images/767-1.png)

虽然功能还比较简单，而且后来还有一个黑色的cmd窗口，但是起码证明这种方式是可行的。而且把它们打成一个zip包之后，大小不到10M，也在可以接受的范围内。

非常开心：）

## 打包方法

忘了说，使用的是一个叫py2exe的程序搞定的，官网：[http://www.py2exe.org/](http://www.py2exe.org/)

用法见：[http://www.py2exe.org/](http://www.py2exe.org/)

1. 首先保证所写的python程序可以正常执行

2. 安装py2exe

3. 写一个setup.py文件，放到要打包的程序的同级，内容如下：

> from distutils.core import setup      
> import py2exe
> 
> setup(console=['hello.py'])

其中的hello.py就是要打包的文件

4. 执行

> <font style="background-color: #ffffff">python setup.py py2exe</font>

期间如果提示说找不到msvcp90.dll这个文件，可以从别处找一个，放到python27/DLLs目录下

5. 如果一切正常，则会在当前目录下生成一个dist目录，里面包含了所有需要的文件

6. 把dist目录拷贝到一台没有安装python/qt的电脑上，看是否能正常运行
