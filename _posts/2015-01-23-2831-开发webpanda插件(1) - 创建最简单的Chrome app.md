---
layout: post
id: 2831
alias: webpanda-dev-create-simplest-chrome-app
tags: webpanda chrome
date: 2015-01-23 16:29:48
title: 开发webpanda插件(1) - 创建最简单的Chrome app
---

我们的插件是一个桌面程序，为了快速实现它，我们打算使用Chrome的App打包功能。

观察Postman的效果
================

它到底能不能实现我们想要的效果呢？我们查看了一个已有的Chrome App: [Postman Packaged App](https://chrome.google.com/webstore/detail/postman-rest-client-packa/fhbjgbiflinjbdggehcddcbncdddomop)

它的效果让我们很满意：

1. 看起来像是一个桌面程序，而不是网站
2. 安装后，在桌面的Dock上，会有一个google提供的应用程序图标，点开后可以找到运行postman
3. 可以通过Spotlight Search功能找到它
4. 它拥有本地程序的各种权限，比如操作文件等

需要什么
=======

由于我们从来没有做过Chrome App，所以第一个任务是做一个最简单的Chrome App：显示一个Html页面，上面有`Hello world`两字。

这里是官方的教程：<https://developer.chrome.com/apps/first_app>

看起来很简单，我们只需要一个manifest描述文件，一个background script文件，和一些图标即可。

- Manifest格式：<https://developer.chrome.com/apps/manifest>

创建必需文件
==========

按照上面的教程，只要创建以下文件，并且贴上内容，就可以了。

```
|-- background.js
|-- icons
|   |-- calculator-128.png
|   `-- calculator-16.png
|-- manifest.json
`-- window.html
```

比预想中的简单太多。

在本地安装
========

安装也很方便，直接在Settings -> Extensions -> Load unpacked extension，选择文件所在的目录即可。

提示成功后，不需要重启，就可以在Dock上的google apps中看到，点击后，马上出现一个看起来非常像桌面程序的窗口出现，上书`Hello World`，太酷了。

改成web panda的信息
=================

然后我们在它的基础上，修改了一些文字和图标，变成我们需要的Web Panda信息。

上传到Chrome web store失败
=========================

本想上传到Chrome web store上，但是那边没有开通中国的付款，无法交$5的注册费。搜了一下，感觉还挺麻烦的，所以先不上传，等弄得差不多了再上传。


