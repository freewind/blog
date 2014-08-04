---
layout: post
title: 在linux上安装postgresql详解
tags:
  - Linux
date: 2011-10-28 21:11:30
---

**下载及安装**

进入源代码下载页面: 

> [http://www.postgresql.org/ftp/source/](http://www.postgresql.org/ftp/source/)

我选择的是v8.4.8: 

> wget [http://wwwmaster.postgresql.org/redir/418/h/source/v8.4.8/postgresql-8.4.8.tar.gz](http://wwwmaster.postgresql.org/redir/418/h/source/v8.4.8/postgresql-8.4.8.tar.gz "http://wwwmaster.postgresql.org/redir/418/h/source/v8.4.8/postgresql-8.4.8.tar.gz")

解压

> tar xzvf postgresql-8.4.8.tar.gz

使用默认参数configure 

> cd postgresql-8.4.8      
> ./configure

<span id="more-515"></span>
<p>如果提示找不到readline，但readline已经安装，说明缺少了readline-devel。其它错误类似。 

> yum install readline-devel

编译安装，过程漫长 

> make &amp;&amp; make install

自动安装到了/usr/local/pgsql下。 

**增加postgresql专用用户**

posgresql为安全考虑，不允许以root用户运行，必须为它建立对应的用户和组 

> useradd postgres

将自动建立对应的组(postgres) 

为其配置环境变量： 

> vim ~postgres/.bash_profile

在最后加上

> PGLIB=/usr/local/pgsql/lib      
> PGDATA=$HOME/data       
> PATH=$PATH:/usr/local/pgsql/bin       
> MANPATH=$MANPATH:/usr/local/pgsql/man       
> export PGLIB PGDATA PATH MANPATH

**建立数据目录**

先切换用户 

> su – postgres

建立数据目录 

> mkdir data

初始化目录数据 

> cd data      
> initdb

将出现如下图：

[![image](http://freewind.me/wp-content/uploads/2011/10/image_thumb.png "image")](http://freewind.me/wp-content/uploads/2011/10/image.png) 

启动postgresql

> posgtres -D /home/postgres/data

如果出现类似下面的提示，就说明安装成功

> LOG:  database system was shut down at 2011-10-28 08:22:40 CST     
> LOG:  database system is ready to accept connections      
> LOG:  autovacuum launcher started

按ctrl+c停止，使用以下命令，以后台服务方式启动

> pg_ctl start

会出现一些提示信息。按回车，将出现命令提示符。

**使用命令行连接数据库**

创建数据库mydb

> <p>$createdb mydb

连接数据库 

> $psql mydb

操作数据库

[![image](http://freewind.me/wp-content/uploads/2011/10/image_thumb1.png "image")](http://freewind.me/wp-content/uploads/2011/10/image1.png) 

说明现在的数据库可以正常运行。 

**允许远程连接**

postgresql因为安全方面的考虑，默认情况下只接受本机的连接。为了管理的方便，我们需要开通远程连接。 

修改data/postgresql.conf，增加： 

[![image](http://freewind.me/wp-content/uploads/2011/10/image_thumb2.png "image")](http://freewind.me/wp-content/uploads/2011/10/image2.png) 

修改监听端口： 

[![image](http://freewind.me/wp-content/uploads/2011/10/image_thumb3.png "image")](http://freewind.me/wp-content/uploads/2011/10/image3.png) 

修改data/pg_hba.conf，增加md5那一行： 

[![image](http://freewind.me/wp-content/uploads/2011/10/image_thumb4.png "image")](http://freewind.me/wp-content/uploads/2011/10/image4.png) 

然后重启posgtres。 

修改配置后马上生效不重启： 

> pg_ctl reload

**给postgres设置密码**

默认情况下，postgresql数据库中的超级用户postgres密码为空，导致无法远程登录。我们必须先给它设个密码： 

[![image](http://freewind.me/wp-content/uploads/2011/10/image_thumb5.png "image")](http://freewind.me/wp-content/uploads/2011/10/image5.png) 

这样，postgres的密码也是postgres了。 

**在windows上安装管理程序**

下载地址： 

> [http://get.enterprisedb.com/postgresql/postgresql-8.4.8-1-windows-binaries.zip](http://get.enterprisedb.com/postgresql/postgresql-8.4.8-1-windows-binaries.zip)

下载后解压，运行其中的pgAdmin III程序，如图： 

[![image](http://freewind.me/wp-content/uploads/2011/10/image_thumb6.png "image")](http://freewind.me/wp-content/uploads/2011/10/image6.png) 

确定后，如果没错误，就会连上。 

[![image](http://freewind.me/wp-content/uploads/2011/10/image_thumb7.png "image")](http://freewind.me/wp-content/uploads/2011/10/image7.png) 

把postgres服务设为自动启动   
进入postgresql的源代码解压目录 

> cp contrib/start-scripts/linux /etc/init.d/postgresql      
> chmod a+x /etc/init.d/postgresql       
> chkconfig –add /etc/init.d/postgresql

好了。 

**在postgresql中创建用户，并设置数据库**

> create database ttt owner ttt;

这样就设置了一个数据库叫ttt，并且它的拥有者是一个叫ttt的用户。