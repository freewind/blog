---
layout: post
id: 2580
alias: write-idea-plugin-yourself-pubeditor-4-pubspec-syntax
tags: Dart
date: 2014-04-10 22:55:21
title: 自己动手写IDEA plugin – PubEditor (4) pubspec.yaml文件语法
---

pubspec.yaml在本质上是一个YAML文件，所以它的语法遵守YAML的规定。但同时它的内容又比较简单，因为它规定了哪些配置项是有效的，其它的都会被自动忽略。

全部有效选项如下：

```yaml
name: newtify
version: 1.2.3
description: >
  Have you been turned into a newt?  Would you like to be?
  This package can help: it has all of the
  newt-transmogrification functionality you've been looking
  for.
author: Nathan Weizenbaum <nweiz@google.com>
homepage: http://newtify.dartlang.org
documentation: http://docs.newtify.com
dependencies:
  efts: '>=2.0.4 <3.0.0'
  transmogrify: '>=0.4.0 <1.0.0'
dev_dependencies:
  unittest: '>=0.6.0'
dependency_overrides:
  transmogrify:
    path: ../transmogrify_patch/
```

其中只有`name`项是在所有情况下都必须的，`version`和`description`只有当要发布时才是必须的，其它的都是可选的。除它们以外的内容都会被忽略。

上面是一个简化的模板，实际上很多选项还有更复杂的用法。更详细的内容，请参考官方文档：[https://www.dartlang.org/tools/pub/pubspec.html](https://www.dartlang.org/tools/pub/pubspec.html)

我原以为IDEA中有可直接使用的YAML插件，但是发现竟然没有！而YAML语法实际上还有比较复杂的，对于我这次的任务来说，提供完整的YAML支持有点不太现实，所以我打算仅仅按pubspec.yaml的定义来实现部分YAML功能。
