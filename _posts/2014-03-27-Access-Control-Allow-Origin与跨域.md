title: Access-Control-Allow-Origin与跨域
tags:
  - 未分类
date: 2014-03-27 20:24:31
---

今天与萌萌一起修bug，遇到一个跨域的问题。我们两个都对它有一些不太清楚，一起搞清楚后记录下来。

## 问题

在某域名下使用Ajax向另一个域名下的页面请求数据，会遇到跨域问题。另一个域名必须在response中添加`Access-Control-Allow-Origin`的header，才能让前者成功拿到数据。

这句话对吗？如果对，那么流程是什么样的？

## 跨域

怎样才能算跨域？协议，域名，端口都必须相同，才算在同一个域。

参考：

*   [Are different ports on the same server considered cross-domain? (Ajax-wise)](http://stackoverflow.com/questions/1077218/are-different-ports-on-the-same-server-considered-cross-domain-ajax-wise)
*   [同事李栋的博客：跨源资源共享](http://twlidong.github.io/blog/2013/12/22/kua-yuan-zi-yuan-gong-xiang-cross-origin-resource-sharing-cors/)

## 当跨域访问时，浏览器会发请求吗

这是真正困扰我们的问题，因为我们不清楚浏览器会怎么做。它会不会检查到你要请求的地址不是同一个域的，直接就禁止了呢？

我在jsbin上[做了一个试验](http://jsbin.com/fusaweqe/1/edit)，使用Chrome打开。当点击“Run with Js”时，控制台上会打出：

    XMLHttpRequest cannot load http://google.com/. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://run.jsbin.io' is therefore not allowed access. 
    

    但开发者工具的&#8221;Network&#8221;栏并没有任何记录。它到底发请求了没？

    我又使用`python -m SimpleHTTPServer`在本地创建了一个小服务器，然后把地址改成它，结果发现在python这边的确打印出请求来了，可见浏览器的确发出了请求。

    ## Access-Control-Allow-Origin

    现在该`Access-Control-Allow-Origin`出场了。只有当目标页面的response中，包含了`Access-Control-Allow-Origin`这个header，并且它的值里有我们自己的域名时，浏览器才允许我们拿到它页面的数据进行下一步处理。如：

    Access-Control-Allow-Origin: http://run.jsbin.io
    

    如果它的值设为`*`，则表示谁都可以用：

    Access-Control-Allow-Origin: *

没错，在产品环境中，没人会用`*`

你可以阅读下面这篇文章了解更多，并可找到其中的&#8221;Run Sample&#8221;链接，实际体验一下：

[http://www.html5rocks.com/en/tutorials/cors/](http://www.html5rocks.com/en/tutorials/cors/)