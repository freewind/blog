---
layout: post
title: 折腾Fedora之五 wine以及windows程序
tags:
  - Linux
date: 2011-09-12 23:31:00
---

在Fedora中，使用QQ是一件相当麻烦的事情。腾讯在09年出了一个满是bug的linux版QQ之后，就再也不管了。我费尽力气把它安装成功之后，再也不想运行它，宁愿使用webqq或air版的QQ。

现在我想常试一下，使用wine能否在linux下正常使用QQ2010或2011。经过一整天的试验，结论是无法在linux上运行QQ（天杀的TX，逼着我们用webqq）。

意外发现是，很多windows程序还是可以正常运行，比如一些小游戏zuma，以及植物大战僵尸（就是太卡了）

一、安装wine

```
$ sudo yum install wine
```

共下载15M左右的程序

二、一个辅助程序winestricks

```
$ wget http://www.kegel.com/wine/winetricks
$ chmod +x winetricks
$ ./winetricks

```

它会提示安装一个gecko的引擎，安装成功后，会出现一个窗口，可以让我们选择一些选项，如安装一些windows下的组件和库等。

三、wine乱码

如果程序上有中文，会出现乱码，变成一个个的小方块，可惜我弄了一天也没办法解决。

四、运行.exe程序

```
$ wine zuma.exe
```

如果缺少库，它会在窗口上提示，我们只需要使用winestricks下载相应的库即可。正常情况，那些exe程序会运行，比如祖玛这个游戏。

五、QQ安装不上，迅雷没试，但是从网上的文章来看，问题也是很多。所以我觉得，最好还是安装一个虚拟机，用来解决这些问题。