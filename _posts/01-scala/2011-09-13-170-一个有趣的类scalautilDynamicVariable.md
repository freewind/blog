---
layout: post
id: 170
alias: an-interesting-class-scala-util-dynamicvariable
tags: Scala
date: 2011-09-13 21:41:00
title: 一个有趣的类scala.util.DynamicVariable
---

该文章以前发表在scala群（132569382）的论坛里，现在拿出来放在博客里。

刚在看scalatra源代码时，发现这么一段：

```scala
protected val _response = new DynamicVariable[HttpServletResponse](null)
protected val _request = new DynamicVariable[HttpServletRequest](null)
```

里面用到了一个叫DynamicVariable的类，为什么要用它呢？经过一翻研究，发现这个类比较重要，很适合用于做context.

首先看一下这个类的api文档，发现它类似于一个栈，不过已经处理好了多线程的调用问题。它有一个方法叫withValue()，很常用，我们一般都这么用它：

```scala
someDynamicVar.withValue(newValue) {
    someDynamicVar.value // -> 得到的就是newValue
}
```

先看一个简单的例子，熟悉一下用法：

```scala
val context = new scala.util.DynamicVariable[String]("000")
println(context) // -> DynamicVariable(000)
context.withValue("111") {
    println(context) // -> DynamicVariable(111)
}
println(context) // -> DynamicVariable(000)
```

从中可以看到，在withValue后面块中，访问context时，它的值是新值。而过了块以后，值又变成了原来的值。

那么，这对我们有什么用呢？

我在stackoverflow上看到一个问题，解答了我的疑惑，原帖可见：

[http://stackoverflow.com/questions/2629367/scala-closure-context](http://stackoverflow.com/questions/2629367/scala-closure-context)

看完这个问题，以及它的回答，就差不多明白了。

还是把例子抄过来吧：

**问题：** 

在groovy中，闭包中的代码，似乎可以知道它正在被谁调用。所以，我们才可以写出下面的代码：

```groovy
swing = new SwingBuilder()
frame = swing.frame(title:'Demo') {
  menuBar {
    menu('File') {
      menuItem 'New'
      menuItem 'Open'
    }
  }
  panel {
    // ...
  }
}
```

menuBar知道自己在哪个frame中，menu知道自己在哪个menuBar中，menuItem知道自己在哪个menu中，这样才不会乱套。在scala中，能不能做到同样的效果呢？

答案当然是可以，关键就是这个DynamicVariable，看以下代码：

```scala
import scala.util.DynamicVariable
import javax.swing._

object SwingBuilder {
  case class Context(frame: Option[JFrame], parent: Option[JComponent])
}

class SwingBuilder {
  import SwingBuilder._
  val context = new DynamicVariable[Context](Context(None,None))

  def frame(title: String)(f: =>Unit) = {
    val res = new JFrame(title)
    res.add(new JPanel())
    context.withValue(Context(Some(res),context.value.parent)){f;res}
  }

  def menuBar(f: =>Unit) = {
    val mb = new JMenuBar()
    context.value.frame.foreach(_.setJMenuBar(mb))
    context.withValue(Context(context.value.frame,Some(mb))){f;mb}
  }

  def menu(title: String)(f: =>Unit) = {
    val m = new JMenu(title)
    context.value.parent.foreach(_.asInstanceOf[JMenuBar].add(m))
    context.withValue(Context(context.value.frame,Some(m))){f;m}
  }

  def menuItem(title: String) = {
    val mi = new JMenuItem(title)
    context.value.parent.foreach(_.asInstanceOf[JMenu].add(mi))
  }
}

object Test {
  def main(args: Array[String]) {
    val builder = new SwingBuilder()
    import builder._

    val f = frame("Demo") {
      val mb = menuBar {
        menu("File") {
          menuItem("New")
          menuItem("Open")
        }
      }
    }
    f.setVisible(true)
  }
}
```
