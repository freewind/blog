---
layout: post
title: "为什么通不过编译 class X extends Map[String,String]"
tags:
  - Scala
date: 2011-09-13 22:21:00
---

我想定义一个类，继承自HashMap，并增加一些额外方法，用于保存一些配置数据。我开始写成：

```
class X extends Map[String, String] {}
```

这段代码不能通过编译，提示说：X应该是abstract的。

scala中两个叫Map的，一个是trait，一个是object。我们平时使用`Map()`来生成一个新的空Map，实际上调用的是object Map的apply()函数。而这里想要继承，extends Map[String,String]，继承的是trait Map。

并且，这个trait Map还是abstract的，里面有几个方法没有实现。所以提示X应该是abstract的才行。

明白之后，改成:

```
import scala.collection.immutable.HashMap

class X extends HashMap[String, String] {}
```

没有问题了。

然后，我想给它增加一个方法，比如叫createEmpty()，返回一个新的空X。写成：

```
class X extends HashMap[String,String] {
    def createEmpty():X {
         X() // 这里有误
    }
}
```

我还想着`Map()`呢，所以这里写成了X()，显示不能能过。HashMap可没有定义`apply()`函数，正确的写法应该是`new X()`。

最后，发现这个createEmpty()，应该放在object中，它应该是一个(java中的)static方法，不能放在class里，所以呢，应该这样：

```
import scala.collection.immutable.HashMap
class X extends HashMap[String, String]
object X { def createEmpty() = new X }
```