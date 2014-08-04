title: 在scala中实现using自动关闭资源
tags:
  - Scala
date: 2011-09-13 21:48:00
---

这是在scalatra中看到的一段代码，觉得非常好。它定义了一个using方法，可以这样使用：

```
using(resouse) { resourse =>
    // do something with the resourse
}
```

当块结束后，resourse的close函数将自动被调用。能够当resourse的对象，必须拥有一个close()函数。使用这种方式，可以使资源在用完后自动关闭，减少我们出错的机会。

 <span id="more-173"></span>
<p>代码如下：

```
package org.scalatra

package object util {
  /**
   * Executes a block with a closeable resource, and closes it after the block runs
   *
   * @tparam A the return type of the block
   * @tparam B the closeable resource type
   * @param closeable the closeable resource
   * @param f the block
   */
  def using[A, B <: { def close(): Unit }](closeable: B)(f: B => A) {
    try {
      f(closeable)
    }
    finally {
      if (closeable != null)
        closeable.close()
    }
  }
}
```

从中可以看到这一句运用了duck type，定义了一个拥有close()函数的type

```
B <: { def close(): Unit } 
```

这样，不管是什么对象，只要它拥有一个close():Unit的函数，都可以在这里使用，并且这个close最后将被自动调用。