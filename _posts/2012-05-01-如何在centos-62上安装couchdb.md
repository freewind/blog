---
layout: post
alias: how-to-install-couchdb-on-centos-6.2
tags: Linux
date: 2012-05-01 20:58:29
title: 如何在centos 6.2上安装couchdb?
---

Couchdb: [http://couchdb.apache.org/](http://couchdb.apache.org/)

首页的Download中，提供了Source供安装，没想到在linux上安装起来麻烦无比，要先安装一大堆各种各样的依赖，我折腾了近两个小时也没搞定。

我的linux是centos 6.2，直接：

> <font style="background-color: #ffffff">yum install couchdb</font>

会报找不到该包，我以为只能能过源代码安装呢。后来发现，只要把EPEL库添加到yum中，就可以直接通过该命令安装了。

打开该页：[http://mirrors.sohu.com/fedora-epel/6/i386/repoview/epel-release.html](http://mirrors.sohu.com/fedora-epel/6/i386/repoview/epel-release.html)

可看到有一个： epel-release-6.5.noarch的链接，这就是我们需要安装的。

> <font style="background-color: #ffffff">$ </font>wget [http://mirrors.sohu.com/fedora-epel/6/i386/epel-release-6-5.noarch.rpm](http://mirrors.sohu.com/fedora-epel/6/i386/epel-release-6-5.noarch.rpm "http://mirrors.sohu.com/fedora-epel/6/i386/epel-release-6-5.noarch.rpm")
> 
> <font style="background-color: #ffffff">$ sudo rpm -install epel-release-6-5.noarch.rpm</font>

即可。如果需要重装，加上`-force`参数即可。

提示成功后：

> <font style="background-color: #ffffff">$ sudo yum makecache</font>
> 
> <font style="background-color: #ffffff">$ sudo install couchdb</font>

会提示一大堆依赖文件，一共有几十M。输入y下载并安装。

安装完成后，可以修改其配置文件：

> <font style="background-color: #ffffff">vi /etc/couchdb/local.in</font>

如文档最大尺寸、端口、地址、验证、日志、vhosts设置以及管理员密码等。

启动：

> <font style="background-color: #ffffff">sudo service couchdb start</font>

linux重启时自动启动：

> <font style="background-color: #ffffff">sudo chkconfig -level 345 couchdb on</font>

验证是否启动成功：

> <font style="background-color: #ffffff">curl [http://locahost:5984](http://locahost:5984)</font>

如果返回如: 

> {"couchdb":"Welcome","version":"1.0.3"}     
> 
>  

这样的json，则说明成功。这里安装的版本是1.0.3，当前官网最新版本是1.2，先将就着用吧。
