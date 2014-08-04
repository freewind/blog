---
layout: post
title: 在linux上安装nginx
tags:
  - Linux
date: 2011-09-22 00:13:11
---

nginx是一个高性能的web server，它还有一个非常好用的功能叫反向代理。考虑到这种情况：我在一台服务器上，要用tomcat跑java程序，还要运行php程序，80端口只有一个，怎么办给谁好呢？使用nginx就可以方便地解决这个问题：让nginx监听80，其它程序各自监听自己的端口，然后在nginx内部配置反向代理，指向它们即可。对于用户的访问，我们可以在nginx通过判断域名或者请求的内容，进行不同的转发，让各请求达到正确的目的地。

下面是在linux安装nginx的步骤：

<span id="more-336"></span>
<p>**下载**

从官网上下载新版本，这里以0.8.45为例：

<pre class="csharpcode">wget [http://www.nginx.org/download/nginx-0.8.45.tar.gz](http://www.nginx.org/download/nginx-0.8.45.tar.gz)```

安装：

<pre class="csharpcode">./configure
make &amp;&amp; make install```

nginx将安装到/usr/local/nginx下

**建立管理脚本**

为了方便管理nginx，我们将建立/etc/init.d/nginx，定义一些如start/stop/status/restart等任务，方便管理。

<pre class="csharpcode">vim /etc/init.d/nginx```

输入以下内容：

<pre class="csharpcode">#!/bin/sh 
# 
# nginx - <span class="kwrd">this</span> script starts and stops the nginx daemin 
# 
# chkconfig:   - 85 15 
# description:  Nginx <span class="kwrd">is</span> an HTTP(S) server, HTTP(S) reverse \ 
#               proxy and IMAP/POP3 proxy server 
# processname: nginx 
# config:      /usr/local/nginx/conf/nginx.conf 
# pidfile:     /usr/local/nginx/logs/nginx.pid 

# Source function library. 
. /etc/rc.d/init.d/functions 

# Source networking configuration. 
. /etc/sysconfig/network 

# Check that networking <span class="kwrd">is</span> up. 
[ <span class="str">"$NETWORKING"</span> = <span class="str">"no"</span> ] &amp;&amp; exit 0 

nginx=<span class="str">"/usr/local/sbin/nginx"</span> 
prog=$(basename $nginx) 

NGINX_CONF_FILE=<span class="str">"/usr/local/nginx/conf/nginx.conf"</span> 

lockfile=/var/<span class="kwrd">lock</span>/subsys/nginx 

start() { 
    [ -x $nginx ] || exit 5 
    [ -f $NGINX_CONF_FILE ] || exit 6 
    echo -n $<span class="str">"Starting $prog: "</span> 
    daemon $nginx -c $NGINX_CONF_FILE 
    retval=$? 
    echo 
    [ $retval -eq 0 ] &amp;&amp; touch $lockfile 
    <span class="kwrd">return</span> $retval 
} 

stop() { 
    echo -n $<span class="str">"Stopping $prog: "</span> 
    killproc $prog -QUIT 
    retval=$? 
    echo 
    [ $retval -eq 0 ] &amp;&amp; rm -f $lockfile 
    <span class="kwrd">return</span> $retval 
} 

restart() { 
    configtest || <span class="kwrd">return</span> $? 
    stop 
    start 
} 

reload() { 
    configtest || <span class="kwrd">return</span> $? 
    echo -n $<span class="str">"Reloading $prog: "</span> 
    killproc $nginx -HUP 
    RETVAL=$? 
    echo 
} 

force_reload() { 
    restart 
} 

configtest() { 
  $nginx -t -c $NGINX_CONF_FILE 
} 

rh_status() { 
    status $prog 
} 

rh_status_q() { 
    rh_status >/dev/<span class="kwrd">null</span> 2>&amp;1 
} 

<span class="kwrd">case</span> <span class="str">"$1"</span> <span class="kwrd">in</span> 
    start) 
        rh_status_q &amp;&amp; exit 0 
        $1 
        ;; 
    stop) 
        rh_status_q || exit 0 
        $1 
        ;; 
    restart|configtest) 
        $1 
        ;; 
    reload) 
        rh_status_q || exit 7 
        $1 
        ;; 
    force-reload) 
        force_reload 
        ;; 
    status) 
        rh_status 
        ;; 
    condrestart|<span class="kwrd">try</span>-restart) 
        rh_status_q || exit 0 
            ;; 
    *) 
        echo $<span class="str">"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"</span> 
        exit 2
esac```

其中这一句

<pre class="csharpcode">nginx=<span class="str">"/usr/local/sbin/nginx"</span>```

需要根据你的安装的情况来选择nginx执行文件的路径。

然后给它加上运行权限:

<pre class="csharpcode">chmod +x /etc/init.d/nginx```

通过这个脚本，我们可以方便的管理nginx，如：

<pre class="csharpcode">/etc/init.d/nginx start|stop|status|restart|reload|force_reload|configtest|rh_status|rh_status_q```

这时我们先检查一下这个脚本和nginx的配置文件有没有问题：

<pre class="csharpcode">/etc/init.d/nginx configtest```

如果输出全部为successful，说明没有问题。

**设为开机启动**

<pre class="csharpcode">/sbin/chkconfig nginx on```

检查一下：

<pre class="csharpcode">/sbin/chkconfig --list nginx 
nginx   0:off   1:off   2:on    3:on    4:on    5:on    6:off```

下次重启时，nginx将自动运行

**修改配置，每个配置文件定义一个虚拟主机**

默认情况下，nginx提供了一个/usr/local/nginx/conf/nginx.conf这个配置文件。如果我们把所有的配置都写在这个文件中，不易管理，所以打算为每一个虚拟主机弄一个单独的配置文件，然后在nginx.conf把它们包含进去。

<pre class="csharpcode">cd /usr/local/nginx/conf 
mkdir conf.d```

这个conf.d用来放置单独的虚拟主机配置。

修改nginx.conf，在http节点中增加下面这行：

<pre class="csharpcode">include /usr/local/nginx/conf/conf.d/*.conf;```

注意最后的分号。

同时把里面的server节点内容全部注释掉。

在/conf.d/下新建default_server.conf，内容如下：

<pre class="csharpcode">server { 
    listen       80 default_server; 
    server_name  localhost; 

    location / { 
        root   html; 
        index  index.html index.htm; 
    } 

    error_page   500 502 503 504  /50x.html; 
    location = /50x.html { 
        root   html; 
    } 
}```

这个配置用于当找不到与请求的url相对应的信息时。

为abc.com建立/conf.d/abc.dom.conf，内容如下：

<pre class="csharpcode">upstream abc.com { 
    server 127.0.0.1:3000; 
    server 127.0.0.1:3001; 
    server 127.0.0.1:3002; 
    } 

server { 
    listen   80; 
    server_name www.abc.com; 
    rewrite ^/(.*) http:<span class="rem">//abc.com permanent; </span>
       } 

server { 
    listen   80; 
    server_name abc.com; 

    access_log /home/www/abc.com/log/access.log; 
    error_log /home/www/abc.com/log/error.log; 

    root   /home/www/abc.com/site/; 
    index  index.html; 

    location / { 
          proxy_set_header  X-Real-IP  $remote_addr; 
          proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for; 
          proxy_set_header Host $http_host; 
          proxy_redirect off; 

          <span class="kwrd">if</span> (-f $request_filename/index.html) { 
               rewrite (.*) $1/index.html <span class="kwrd">break</span>; 
          } 

          <span class="kwrd">if</span> (-f $request_filename.html) { 
               rewrite (.*) $1.html <span class="kwrd">break</span>; 
          } 

          <span class="kwrd">if</span> (!-f $request_filename) { 
               proxy_pass http:<span class="rem">//abc.com; </span>
               <span class="kwrd">break</span>; 
          } 
    } 
}```

配置内容还是比较容易看懂的。从中可以看出，首先通过upstream定义了thin的几个服务端口（转发目标），下面是把对于abc.com和[www.abc.com](http://www.abc.com)的请求，转发给那几个端口。

对于def.com，可以生成一个conf.d/def.com.conf，把上面的内容复制进去，修改目标端口和相关的域名及路径。

然后使用

<pre class="csharpcode">/etc/init.d/nginx configtest```

看看有没有错。

**启动nginx**

<pre class="csharpcode">/etc/init.d/nginx start```

然后打开浏览器，访问http://www.abc.com和[http://www.def.com](http://www.def.com)，正常情况下应该能成功打开对应的网站。如果不行，请检查：

1.  域名是否指向了正确的ip
<li>[http://ip:80](http://ip:80)能不能访问。如果打不开，说明nginx没有启动成功
<li>[http://ip:3000](http://ip:3000)能不能访问。如果不能，说明thin没有启动成功
<li>如果都能访问，说明nginx关于虚拟主机的配置有问题，仔细检查路径和名字

大功告成！