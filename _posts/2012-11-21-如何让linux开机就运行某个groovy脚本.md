---
layout: post
title: 如何让linux开机就运行某个groovy脚本
tags:
  - Linux
date: 2012-11-21 20:37:50
---

看起来这是一个比较简单的问题，但我这么多年硬是不会。今天校友网又挂了，老师又给我留言，让我及时解决。

于是花了几个小时，用groovy写了一个脚本，其作用就是每隔一分钟访问一次首页，如果连不上或者内容不对，就会依次检查数据库及网站服务，在需要的时候重启。只要把这个脚本跑起来，就不用担心了。

不过如何让服务器重启后这个脚本也能自动运行呢？经过半天的摸索和十来次的重启，终于试验成功。

首先，要装上groovy，并在/usr/local/bin下，把groovy链过去：

> <font style="background-color: #ffffff">cd /usr/local/bin</font>
> 
> <font style="background-color: #ffffff">ln -s ~/dev/groovy/bin/groovy groovy</font>

我的监控脚本位于：

> <font style="background-color: #ffffff">/www/monitor.groovy</font>

然后在/etc/init.d下建立一个web_monitor的文件，内容如下：

> <font style="background-color: #ffffff">#!/bin/sh</font>
> 
> <font style="background-color: #ffffff">#chkconfig:2345 80 05</font>
> 
> <font style="background-color: #ffffff">PATH=/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin</font>
> 
> <font style="background-color: #ffffff">groovy /www/monitor.groovy</font>

这个文件大有文章。

1.  第一行表示使用sh来调用该脚本
2.  第二行用于chkconfig命令，设置了一些参数，这里有点复杂我也不太懂，不过大多数情况照这个写就可以了。
3.  第三行非常重要，我十几次重启都是因为它。如果不加上这个path，下面的groovy命令就找不到，自然就没有办法启动成功了
4.  最后一行调用我那个监控脚本

然后要把这个文件加到启动列表里：

> <font style="background-color: #ffffff">chmod +x web_monitor</font>
> 
> <font style="background-color: #ffffff">chkconfig -add web_monitor</font>
> 
> <font style="background-color: #ffffff">chkconfig -list web_monitor</font>

如果没有意外，就行了。然后reboot，看看它有没有运行成功