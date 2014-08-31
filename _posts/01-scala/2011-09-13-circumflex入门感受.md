---
layout: post
alias: try-circumflex
tags: Scala
date: 2011-09-13 22:03:58
title: circumflex入门感受
---

使用circumflex大约一周，通过边写代码边写测试的方式，到今天终于把它的orm,mvc等基本功能跑通。感觉如下：
<p>1. orm模块：api设计巧妙，用起来很顺手。提供了类似于SQL的DSL来写查询，在编译器的实时查错的帮助下，不易出错。源代码中注释和文档写得很好，连我这刚学scala半个月的新手，读起来都不觉得费力。在测试时，可以方便地根据定义的model，去生成表，还可以在xml中定义测试数据，导进去。有一点需要注意，官方现在仅支持postgresql数据库，其它数据库的支持需自己扩展或搜索。
<p>2. mvc模块：结构很简单，controller+action，再render view层，就搞定了。其中router那一块，可以很灵活地支持各种结构的url。view层支持freemarker及scalate。这一块很简单，基本上看两眼就知道大概了。
<p>3. view层：我使用的是scalate，jade语法。因为我比较喜欢这种类haml的风格，比较顺手。需要先看scalate定义的那些简化符号。
<p>其它的，就是用sbt来控制库依赖、编译、测试及启动jetty，使用scalatest来写测试代码。每写一个类，就马上写它的对应代码，对于学习新框架很有帮助。另外，使用eclipse加scala插件来编码，有点卡，差不多每次保存都需要卡3到5秒，勉强接受。只使用了它的代码着色、格式化及实时查询功能，函数提示基本无视。
<p>期间遇到了一些问题，主要是因为对scala不熟悉，没细看circumflex的文档和api，在google group中问了不少问题，好在开发组很热心，基本上都解决了。
<p>目前感觉还不错。
