---
layout: post
title: 尝试用Kotlin写angularjs
tags: 其它语言
date: 2013-05-15 21:07:00
---

Kotlin在主页上的介绍是这样的：

> Kotlin
> 
> is a statically typed programming language that compiles to JVM byte codes and JavaScript.

可以看出它的卖点之一是生成javascript。

我想尝试用它写angularjs程序，如果能够顺利实现，以后就尝试用它同时写前后端。

目前的感觉是Kotlin在语法层面还是比较死板，在支持angularjs中一些比较灵活的写法时，代码相当难看。

先看这一段很常见的标准的directive写法，以下是javascript代码：

```
todomvc.directive('todoFocus', function todoFocus($timeout) {
    return function (scope, elem, attrs) {
        scope.$watch(attrs.todoFocus, function (newVal) {
            if (newVal) {
                $timeout(function () {
                    elem[0].focus();
                }, 0, false);
            }
        });
    };
});

```

再看在kotlin中怎么写：

```
val todoFocus = {(timeout: Timeout) ->
    {(scope: BaseScope, elem: Elem, attrs: Attrs) ->
        scope.`$watch`(attrs.todoFocus, { (newVal: Any) ->
            if(newVal as Boolean) {
                timeout({ elem[0].focus() }, 0, false);
            }
        })
    }
}

```

不能不说Kotlin这种写法，可读性实在太差了。在Kotlin中，对于函数值的支持不太好，而且语法形式也不太好看。它只支持这种声明方式：

```
{ (a:String, b:Int) ->
    // do something
}

```

当嵌套一多，就是前面那种效果。另外kotlin的变量中不能使用`$`，而在angularjs中大量使用，所以我们还需要用反引号包一下。

我希望Kotlin能提供类似javascript中的那种声明方式，即使用fun来声明变量值，这样的话，代码可以写成与javascript版本基本一致，可读性就好很多。

在[http://devnet.jetbrains.com/message/5487375#5487375](http://devnet.jetbrains.com/message/5487375#5487375)上提了个问，看看有没有给出更好的写法，不过目前看来应该是没有了。如果是这样，只能再等等看了，毕竟现在的这种写法，实在让人没法接受。

刚看到这个问题有人回答，给出了一种直接引用函数的写法，如下：

    fun test(): (Int) -> Int {
        fun inner(i: Int) = i
        return ::inner
    }

但在我的最新版的Kotlin上编译不过，听说是一个正在实现中的功能。如果实现的话，的确会有很大程度上解决上面的问题（不过嵌套多的时候，还是有点麻烦，毕竟要起多个名字）。

保持关注。
