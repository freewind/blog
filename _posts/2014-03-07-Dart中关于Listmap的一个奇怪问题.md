---
layout: post
alias: strange-problem-of-list.map-or-dart
tags: Dart
date: 2014-03-07 23:47:04
title: Dart中关于List.map的一个奇怪问题
---

在写Dart时遇到了一个奇怪的问题，为了复现问题，把相关代码简化成下面的例子：

    int id = 0;

    nextId() {
      print("########## currnet id: $id");
      return id++;
    }

    main() {
      var list = [1, 2, 3];
      var users = list.map((n) => new User("user_${nextId()}"));
      for (int i = 0; i < 10; i++) {
        print(users.first.name);
      }
    }

    class User {
      String name;
      User(this.name);
      toString() => name;
    }
    

    我的本意是得到另一个users列表，然后调用它里面的元素。在循环里，反复输出`users.first.name`，直觉上应该都是`user_0`。

    但结果却很意外：

    ########## currnet id: 0
    user_0
    ########## currnet id: 1
    user_1
    ########## currnet id: 2
    user_2
    ########## currnet id: 3
    user_3
    ########## currnet id: 4
    user_4
    ########## currnet id: 5
    user_5
    ########## currnet id: 6
    user_6
    ########## currnet id: 7
    user_7
    ########## currnet id: 8
    user_8
    ########## currnet id: 9
    user_9
    

    看以看到，每次调用的结果竟然都不一样。

    经过反复尝试(过程略)，发现问题出在`map`方法上。很多语言都为List提供了map方法，用于把List中的各元素转换为另一个对象。我一开始以为它返回的是另一个List呢，内容应该是确定的才对。

    打印类型看看：

    print(users.runtimeType)
    

    发现是`MappedListIterable`. 这个类很神秘，在网上和Dart SDK里都找不到它的定义，但从行为上分析，发现它每次调用first，似乎都会重新执行一遍map中的代码。所以每次调用`.first.name`，都会生成一个新的User对象，于是调用了多次`nextId()`。

    查了一下`List.map`的文档，发现它的确是这样的：

    /**
     * Returns a lazy [Iterable] where each element [:e:] of `this` is replaced
     * by the result of [:f(e):].
     *
     * This method returns a view of the mapped elements. As long as the
     * returned [Iterable] is not iterated over, the supplied function [f] will
     * not be invoked. The transformed elements will not be cached. Iterating
     * multiple times over the the returned [Iterable] will invoke the supplied
     * function [f] multiple times on the same element.
     */
    Iterable map(f(E element));
    

    如果想拿到一个确定的List，不要忘了再调用一下`toList()`:

    var users = list.map((n) => new User("user_${nextId()}")).toList();

再运行该代码，发现每次打印的值都是user_0了。
