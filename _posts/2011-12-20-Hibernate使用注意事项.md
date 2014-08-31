---
layout: post
alias: notice-when-using-hibernate
tags: Java
date: 2011-12-20 00:45:48
title: Hibernate使用注意事项
---

因为公司项目需要，无奈还是使用上了Hibernate。我一向认为Hibernate是一个高难度的的框架，原理复杂、问题多多，稍不注意就被卡住，所以一向避而远之。但是因为公司的项目要同时支持Oracle和SqlServer，除了jdbc,与mybatis，能担此重任的也只有hibernate了，最后还是用上了它。当前的版本是3.6.x。

一、使用Annotation还是hbm.xml

使用Annotation很爽，除了一点：列定义的注释信息，在生成数据表时会丢失。因为公司之前的项目都是先生成数据表，列名都是拼音首字母，所以注释显得非常重要。为了保持这一传统，我采用了曲线救国的方式：先使用hibernate tools的ant任务，从annotation生成xml，再写一个工具类，从annotation中读取注释添加到xml中，再通过ant任务，从xml生成数据表。这样注释信息就能成功地加到数据库里去了。

开始表之间的结构比较简单时，这一方法没有问题。直到有一天，我使用了@Inheritance来定义带有继承关系的类，生成的xml中缺少@DiscriminatorColumn字义的字段！这意味着生成的表结构不正确，导致后来插入数据总是出错。我搜索很久没有找到答案，左右为难：难道要放弃Annotation，改用手写的xml吗？工作量可不小，而且很不直观。

这个问题还是没有解决，但是最后我决定不管它了：因为使用另一个ant任务，可以直接将annotation生成正确的数据表。虽然没有了注释信息，有点可惜，但是一来说不定这个功能以后会加上，二来当我们基于类而不是数据表来做功能时，数据表中的注释已经基本不重要了。

hibernate tools提供了多种方式的转换：annotation <-> hbm.xml <-> 数据表，可惜步骤越多越容易出现信息丢失的情况。

最后的结论是：annotation或者hbm.xml，在一开始时就选一种用下去，不要为了解决其中的一个问题把它转换为另一种，因为可能又引发其它的问题。
