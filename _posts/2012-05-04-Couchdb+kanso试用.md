---
layout: post
title: Couchdb+kanso试用
tags:
  - JavaScript
date: 2012-05-04 18:36:18
---

Couchdb中有一个强大的特性，可以把html/css/js等直接嵌入到couchdb中，将couchdb变成webserver+database。Kanso号称couchdb的npm，它可以帮我们做这些事，并且一些主要功能都比较成熟了，相比couchapp更胜一筹。

couchdb的这个功能非常吸引我，我的目标就是前台使用angularjs，后台使用couchdb干所有事。

我花了几天时间来熟悉couchdb的操作以及kanso提供的功能，期间参考了：[https://github.com/petebacondarwin/questionnaire-angular](https://github.com/petebacondarwin/questionnaire-angular)。开始一直比较顺利，直到遇到了用户权限问题。

用户权限问题，是指couchdb提供了http restful的api以供客户端对数据库、文档进行CRUD。如果我们直接通过浏览器端进行操作，如何对用户的权限进行检查与限制呢？虽然couchdb也内置了一些简单的验证方案，但粒度太大，无法细化。

比如couchdb提供了三种不同的用户：

1.  数据库读取者：可以读取某个数据库中的所有数据，但不能修改
2.  数据库管理员：可以读取及修改某个数据库中所有数据
3.  超级管理员：对所有数据库拥有管理权限

从这里可以看到，一个用户至少拥有某数据库**全部数据**的**读取**权限，这就意味着不同用户之间没有秘密可言了。目前没有发现有什么简单的方法可实现更细粒度的控制方法。虽然couchdb正在努力改善，但相信短期内不会有好消息。

那么couchdb的这种方式，岂不是完全无用了？也不是，在某些场合还是可用。因为couchdb可以设置某用户只能读取某个数据库的数据，这样可以为每个用户设置一个单独的数据库，将不同用户之间的数据完全隔开。这种方式对于那些用户之间数据比较独立的程序，比如手机上的通讯录管理等有意义。以我目前的认识，“库”对于couchdb是一个轻量级的概念，有点像其它数据库中的“表”，所以每个用户一个库也是可以接受的。

不过对于通常的网站，这种方式局限性太大。还是得使用传统的做法，在数据库与浏览器端js之间，加一个web层。数据库只能与web层交互，web层只能与浏览器端交互。web层向浏览器端暴露一些接口，并且对浏览器端的请求进行验证。虽然这种方法成熟好用，但意味着我还需要写一个web层，数据之间的交互也多了一次。

希望落空，下面将研究一下，如何使用express+couchdb或者express+mongodb。