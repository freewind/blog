---
layout: post
id: 2062
alias: the-node-webkit-expirement-from-liusong
tags: JavaScript
date: 2013-02-01 11:19:47
title: 刘松的node-webkit体验
---

（以下为Javascript热情交流群中刘松关于node-webkit的聊天记录。）

我在考虑用node-webkit做一个小客户端程序。同时也有web版的。

所以在找node的RPC库。找来找去发现都不合适，都好久没更新，就一个dnode看起来不错，装在我的苹果系统上出现库不兼容的问题。

dnode是基于socket.io的，我想了一下，其实直接用socket.io当RPC库用就可以。

我们要做的功能是，解析本地或服务器上的一个或多个sqlite文件，展现在前台界面上，提供查询、过滤、报表等功能。

除了sqlite文件是放在本地还是服务器这点差别外，其它逻辑（解析sqlite，执行sql，前端界面渲染）都是一样的。

理想情况下，用node-webkit对这个应用打包，就成为本地应用（桌面程序）。或者作为服务器端程序跑。

node-webkit最近发展不错，topcube可以放弃了

看 github上的wiki部分，文档也还好

就是 浏览器的进程和nodejs的进程如何融合，要做的事很多。它主要复杂在这里。

node-webkit做nodejs和浏览器进程的共存处理，不需要开发者关心。但这个工作比较复杂，比如console对象，在nodejs里和浏览器里是不一样的

show一下我刚玩的东西

[![image](http://freewind.me/wp-content/uploads/2013/02/image_thumb.png "image")](http://freewind.me/wp-content/uploads/2013/02/image.png)

[![image](http://freewind.me/wp-content/uploads/2013/02/image_thumb1.png "image")](http://freewind.me/wp-content/uploads/2013/02/image1.png)

[![image](http://freewind.me/wp-content/uploads/2013/02/image_thumb2.png "image")](http://freewind.me/wp-content/uploads/2013/02/image2.png)

node-webkit结合angularjs、sqlite3

node-webkit要求，如果你依赖了native的库，要用一个叫 nw-gyp的工具编译。可是我在mac上编译时生成的东西没好使，因为我是64位系统。最终参考这个里面的第一个回复解决了：   
[https://github.com/rogerwang/node-webkit/issues/296](https://github.com/rogerwang/node-webkit/issues/296)

能打包成绿色可执行程序

总结一下今晚试node-webkit的收获：能结合d3、angularjs前端库，能用sqlite3后端库（需nw-gpy自己编译），能用nodejs官方的net、child_process库，不需要自己编译，直接require就能用。sqlite3的each回调是在下次队列时执行，因为结合angularjs时需要手工调$scope.$digest()，稍有不爽。
