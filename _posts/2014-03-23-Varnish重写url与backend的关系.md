---
layout: post
title: Varnish重写url与backend的关系
tags: 未分类
date: 2014-03-23 20:29:41
---

Varnish是一个开源的反向代理服务器，具有页面缓存功能，也可以用来做负载均衡，据说它的性能比较好。我们的项目中也使用了它。

## 背景

我们有几台不同的服务器，分别运行不同的网站，每个服务器有自己的域名。为了能让用户得到更好的体验，不需要在不同的域名中跳转，我们使用了一台服务器运行Varnish来进行反向代理，根据用户请求的路径分别到不同的服务器上取内容展示给用户。

假设Varnish服务器对应域名为`abc.com`，后端几个网站分别为`x1.com`, `x2.com`, `x3.com`，我们在Varnish中定义了以下映射规则：

    abc.com/products   ->   x1.com/products
    abc.com/admin      ->   x2.com/admin
    abc.com/help       ->   x3.com/help
    

    对于用户来说，他看到的网站都在`abc.com`下，完全不需要知道后台有多少服务器多少独立系统。由于这些系统的页面风格都非常一致，从用户角度上看，似乎就是在访问一个网站。

    ## 新需求

    现在有一个新的需求：

    我们有一个网站部署在另一家服务商，并且可以通过网址：`https://some.server.com/myserver/user/login.html`访问到。为了让用户有一致的体验，要添加一个映射：

    abc.com/login -> https://some.server.com/myserver/user/login.html
    

    ## 分析

    最开始我们觉得这是一个很简单的任务，虽然对Varnish不太了解，但是因为有类似的功能可参考，以为直接复制下代码就能完成。但随着多次尝试失败，发现问题似乎不是这么简单。

    ### Redirect

    开始我们把它当成redirect来处理了，即让server返回一个301，告诉用户新的地址即可。

    在Varnish的配置文件中加入：

    sub vcl_recv {
        if (req.url ~ "^/login") {
            set req.http.Location = "https://some.server.com/myserver/user/login.html";
            error 750 "Permanently moved";
        }
    }

    sub vcl_error {
        if (obj.status == 750) {
          set obj.http.location = req.http.Location;
          set obj.status = 301;
          return (deliver);
        }
    }
    

    第一块检查url后，把`Location`改成目标地址，并且抛一个错误。第二块捕获错误并处理，可以看到它直接把`Location`拿过来，并把`status`设成了301。

    这样用户访问`http://abc.com/login`时，浏览器就会跳转到`https://some.server.com/myserver/user/login.html`页面。

    可是，不合需求！

    看来我们需要的是`rewrite`，而不是`redirect`

    ### rewrite

    参考一些例子后，发现可以按下面的方式来进行rewrite:

    sub vcl_recv {
        if (req.url ~ "^/login") {        
            set req.http.x-host = req.http.host;
            set req.http.x-url = req.url;
            set req.http.host = "www.varnish-cache.org";
            set req.url = "/some-page.html";
        }
    }
    

    结果我发现，这种方式，只是把`host`和`url`作为header中的参数传给了某个`backend`。

    我的default的backend的配置是这样的：

    backend default {
        .host = "127.0.0.1";
        .port = "9999";
    }
    

    它实际上是我用`python -m SimpleHTTPServer 9999`开的一个简单服务器。

    然后当我在浏览器中访问时，发现python这边输出了类似下面这样的日志：

    [23/Mar/2014 20:19:14] "GET /some-page.html HTTP/1.1" 404 -
    

    看来Varnish只能向一个预先定义好的backend上发送请求，并不会“聪明地”根据host的内容去外部网站拿数据。

    ## 猜想的解决方案

    通过文档以及实验，可以确定Varnish在处理请求的转发方面方面，规则的确比较“简单”。

    我们需要事先定义好一些backend，每个backend只能给定一个域名(或IP)和端口，它们必须能够接受HTTP请求。然后我们在配置文件中，设置一些规则，比如：当我们访问某个url时，Varnish以哪种方式检查请求的路径，使用哪个backend（或者默认），同时还可以修改请求的参数。在运行时，Varnish就会把我们发起的请求，转给相应的backend，拿到回复后，再返回给用户。

    可以看到，它只能在已经定义好的backend列表中选择一个，而不会从之外的地方拿数据。

    所以我目前想到的解决方案就是：

1.  首先拿到`https://some.server.com/myserver`对应的ip，或者专门给它配置一个独立的域名，以防止写死IP
2.  在Varnish中为它添加一个backend
3.  判断用户请求的路径，满足条件时转发给该backend

    ### https

    还有一个https问题，因为看到有些文档说Varnish还不支持https，只支持http，不知道最新版怎么样。另外也不知道项目中使用的版本是不是支持，明天看看。

    如果不支持的话，只能改用http协议。

    或者：Apache2似乎支持对https进行反向代理，那可以在后台再加一个Apache，两次反向代理。。。

    * * *

    更新：

    实际上不用配置独立的域名，直接使用`some.server.com`就行，配置一个这样的backend:

    backend someServer {
        .host = "some.server.com";
        .port = "80";
    }
    

    然后在`vcl_recv`中：

    if(req.url ~ '^/login') {
        set req.http.host = "some.server.com";
        set req.url = "/myserver" req.url;
        set req.backend = someServer;
    }

即可。

另外发现不一定要用`https`，所以就直接用80端口了。
