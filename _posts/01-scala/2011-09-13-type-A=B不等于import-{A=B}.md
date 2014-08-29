---
layout: post
title: "type A=B不等于import {A=>B}"
tags: Scala
date: 2011-09-13 22:18:00
---

今天被一个问题折磨了很久，最后才想明白。

在检查代码的时候，发现不少类为了使用mutable的map，都进行了这样的导入:

```
import scala.collection.mutable.{ Map => MMap }
```

然后这样使用：

```
val map = MMap[String,String]
```

于是想，何不把它放到一个package object中，这样就不需要到处导入了。于是：

```
package org
package object scalaeye {
    type MMap[K,V] = scala.collection.mutable.Map[K,V]
}
```

然后去掉了其它类中的import {Map=>MMap}的导入声明。结果发现编译时，所有使用了MMap的类，都报错：找不到type MMap。

 <span id="more-183"></span>
<p>MMap明明已经定义了，为什么找不到呢？简化问题为：

```
class Test {
    type MMap[K,V] = scala.collection.mutable.{Map=>MMap}
    val data = MMap[String,String]()
}
```

还是报找不到MMap，可是明明就在前面定义了！

调试了好久，突然想起之前问过这个问题，答案也差不多想起来了。

```
type MMap[K,V] = scala.collection.mutable.Map[K,V]

import scala.collection.mutable.{ Map => MMap }
```

这两者右边的Map是不一样的。第一个是trait Map，第二个是object Map。第一个像是一个接口，并没有apply()函数可让我们调用，所以MMap()出错。而第二个，实际上还是object Map，我们可以调用它的()函数

下面是唐古拉山的回贴：

```
import scala.collection.mutable.{ Map => MMap }
-> Map trait及其伴生对象都可见了
```

如:

```
classOf[MMap[_,_]]
MMap()
```

scala 知道什么时候是trait，什么时候object 
看看Predef里对immuable.Map的导入

```
...
type Map[A, +B] = collection.immutable.Map[A, B] //类型别名
val Map = collection.immutable.Map //引用对象
...
```
