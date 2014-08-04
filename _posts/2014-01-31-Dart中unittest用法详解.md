---
layout: post
title: Dart中unittest用法详解
tags:
  - Dart
date: 2014-01-31 23:36:53
---

这半年的项目经验告诉我：要想稳打稳扎的学习一门语言，首先应该学习它的测试框架，然后以测试为基础，逐步学习更多的语言细节、每三方库。

这样做的好处是：

1.  我们可以快速尝试并验证新的知识点，得到一个确定的结果
2.  可以留下一份活文档，可随时运行和回顾

如果是一门还不太成熟稳定、API经常变化的语言，还有一个额外的好处：运行一遍测试就知道新版本中破坏了以前的哪些功能，可以立刻更新原有的知识。

我打算重拾Dart，来实现我建立一棵知识树的学习计划，结果发现以前的代码全都跑不起来了，非常郁闷。所以便以此为机会，先把Dart的单元测试框架搞懂。

Dart内置了一个单元测试框架，就叫unittest。它从jUnit中借鉴了不少，尤其是类似于harmcrest的api，如果你熟悉的话，可以很快上手。但由于Dart语言中大量使用Future，所以需要特别学习一下它的异步测试。

这篇文章将分成四个部分：

1.  基础用法
2.  异步测试
3.  自定义matcher等高级用法
4.  内部实现机制（以后补）

边学边写，不断补充。

（如果有同学也对Dart感兴趣，欢迎加入QQ群：322215472）

## Unittest基本用法

一个最简单的单元测试`test.dart`：

    import 'package:unittest/unittest.dart';
    main() {
        test("simple test", () {
            expect(1 + 2, 3);
        });
    }
    

    相应的项目依赖文件`pubspec.yaml`：

    name: learn-dart-unittest
    dependencies:
      unittest: any
    

    ### 命令行运行该测试

    下载依赖：

    pub install
    

    运行测试：

    dart test.dart
    

    输出如下：

    unittest-suite-wait-for-done
    PASS: simple test

    All 1 tests passed.
    unittest-suite-success
    

    ### IDEA dart插件运行

    我使用IDEA中的dart插件运行该测试，会报错。原来必须为`test.dart`指定一个`library`，名字随便写，比如：

    library mytest;

可以参考这个问题：[http://stackoverflow.com/questions/17388363/how-to-run-unittest-with-idea-dart-plugin](http://stackoverflow.com/questions/17388363/how-to-run-unittest-with-idea-dart-plugin)