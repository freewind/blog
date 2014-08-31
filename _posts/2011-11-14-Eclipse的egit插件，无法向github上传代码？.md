---
layout: post
id: 578
alias: cannot-push-code-to-github-with-egit-of-eclipse
title: Eclipse的egit插件，无法向github上传代码？
tags: 未分类
date: 2011-11-14 22:13:19
---

今天使用eclipse时，遇到一个奇怪的问题。我首先通过命令行，导入了一个具有读写权限的项目：

> <font style="background-color: #ffffff">git clone [git@github.com:linqing/ebb.git](mailto:git@github.com:linqing/ebb.git)</font>

然后在它的基础上创建了一个eclipse项目。修改了代码之后，通过eclipse的egit插件提交，总是提示Auth fail，试遍了所有密码都一样。仔细检查了egit相关的配置，都没有发现问题。

可是通过命令行是没有问题的，这是怎么回事？

好在有万能的stackoverflow，经过搜索，发现了这个问题，看来不只我一个人遇到：

> [http://stackoverflow.com/questions/3601805/auth-problem-with-egit-and-github](http://stackoverflow.com/questions/3601805/auth-problem-with-egit-and-github)

 

<span id="more-578"></span>
<p>原来，通过ssh向github提交时，需要配置密钥文件，但这个选项却没有放在egit中，而是在"_**Window > Preferences > Network Connections > SSH2"**这个偏僻的角落里。_

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb10.png "image")](http://freewind.me/wp-content/uploads/2011/11/image10.png) 

关键在于这个ssh2 home，之前的值是另一个目录，里面没有密钥文件。

看看那个目录里有什么？

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb11.png "image")](http://freewind.me/wp-content/uploads/2011/11/image11.png) 

关键就在于id_rsa这个文件，这是私钥。在Key Management中，将它导入：

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb12.png "image")](http://freewind.me/wp-content/uploads/2011/11/image12.png) 

然后再试试提交，成功。
