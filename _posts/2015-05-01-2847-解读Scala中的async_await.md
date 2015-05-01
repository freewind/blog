---
layout: post
id: 2847
alias: understand-scala-future-async-await
tags: scala async await future
date: 2015-05-01 11:46:29
title: 解读Scala中的async/await
---

最近在学习Coursera上的<<Principles of reactive programming>>，在第三周的课程里，我们需要理解Scala中的Future和Promise，因为它们是reactive programming的基础。

里面提到了一对很有趣的方法，叫`async`和`await`，实现了类似C#中的`async/await`关键字（在Javascript的ES7中也将会实现），可以让我们以类似写同步代码的方式写出实际上是异步执行的代码。

这种方式可以大幅提高可读性，减少不必要的代码嵌套和callback，感觉很酷，但是如果不知道它背后是什么原理的话，可能不敢放心使用。

在这里将会一步步介绍它产生的原因，以及基本的实现原理。

Future
------

在Scala中，我们可以使用`Future.apply`，将一段耗时的代码放到一个线程池中异步执行，不会阻塞当前线程。

`Future.apply`除了接收我们传入的耗时代码，还需要一个implicit的`ExecutionContext`（相当于一个线程池）用来执行异步代码。这里我们使用了scala提供的一个global context: `scala.concurrent.ExecutionContext.Implicits.global`

```
import scala.concurrent.ExecutionContext.Implicits.global

println("### start in main thread: " + Thread.currentThread().getName)

Future {
  println("### enter future in pool: " + Thread.currentThread().getName)
  Thread.sleep(2000)
  println("### exit future in pool: " + Thread.currentThread().getName)
}

println("### end in main thread: " + Thread.currentThread().getName)

```

执行这段代码，会得到以下的输出:

```
### start in main thread: main
### end in main thread: main
### enter future in pool: ForkJoinPool-1-worker-5
```

可以看到`### end`在`### enter future`前打印出来（也许不是绝对，但也能说明问题），说明`Future`中的那段耗时代码并不会阻塞当前线程。

通过这样的方式，我们就可以方便地在多个线程中同时执行操作，互不干扰，也不会阻塞当前线程，提高了执行效率。

但显然这样会带来一个问题：我怎么取Future中在未来某个时间点计算出来的值？

通过回调操作Future产生的值
-----------------------

对于异步代码，通常我们会传入一个回调，让异步代码在未来某个时间计算出值后调用它。

`Future`也提供了类似的方法，比如`onComplete`, `onSuccess`, `onFailure`等。

```scala
val pageFuture = Future { getPageFromUrl(...) }
pageFuture.onSuccess { page => 
    println("Got page: " + page)
}
```

对于简单的情况，这样是很方便的。但是如果我们需要对产生的结果再进行一些操作，又得到一些Future，那么很容易产生大量的回调嵌套，导致代码的可读性变差：

```scala
val pageFuture = Future { getPageFromUrl(...) }
pageFuture.onSuccess { page => 
    val tree = parsePage(page)
    tree.onSuccess { tree => 
        val images = findImages(tree)
        images.onSuccess { images =>
            println("Found images: " + images)
        }
    }
}
```

所以`Future`又提供了很多用来组合的方法，让我们不用（或尽量少用）嵌套，比如`map`, `flatMap`, `foreach`等方法。比如上面的代码使用`flatMap`，可以改写为：

```scala
val pageFuture = Future { getPageFromUrl(...) }
pageFuture.flatMap ( page =>  parsePage(page) )
          .flatMap ( tree => findImages(tree) )
          .foreach { images =>
            println("Found images: " + images)
          }
```

可以看到，之前的嵌套回调变成了顺序回调，功能未变，但代码的可读性得到了极大提高。

Await.ready/result
------------------

通过上面的方式，虽然我们可以方便的操作Future产生的值，但可能会出现这种情况：一旦使用了Future，并需要对它在产生的值进行操作，就会发现几乎所有后续代码都会写成异步的形式。

如果想在当前线程中拿到异步的值，该怎么办？

比如测试中，我们需要在主线程中拿到某个Future的值进行判断，虽然可以让测试框架模仿js中的jasmine/mocha那样通过调用一个特别的`done`回调来通知完成，但是如果能直接在主线程中拿到值，代码会更简洁一些。

为了解决这个问题，Scala提供了一个`scala.concurrent.Await`类，可以让我们调用其`ready/result`等方法，阻塞当前线程，直到传入的future完成。`ready`与`result`很像，只是前者不关心返回值只等待，而后者还要返回future中产生的值。

其调用方法如下：

```scala
import scala.concurrent.duration._
import scala.language.postfixOps
import scala.concurrent.{Await, Future}
import scala.concurrent.ExecutionContext.Implicits.global

val future = Future { doSomething(); 42; }
val value = Await.result(future, 2 seconds)
println(value) // 42
```

其中前两句导入，是为了支持下面`2 seconds`这种方便的写法。

`Await.result`那一句，是说我将在当前线程等待`future`的执行，最多只执行`2`秒。如果2秒内执行结束，便马上返回它产生的值`42`；否则会抛出`java.util.concurrent.TimeoutException`。

在等待的过程中，当前线程是阻塞的，后面的代码只能等到`future`成功执行完，才有机会执行。

这种方式可以满足我们的需求，但是不推荐在生产代码中使用。因为一旦使用，就会阻塞当前线程，会让我们在前面辛苦实现的Future的工作白做了。

同时操作多个Future时的代码可读性
----------------------------

虽然Future提供了各种`map`, `flatMap`，让我们在对同一个Future的值进行多次后续操作时变得简单，但是当我们需要同时操作多个Future时，代码依然比较难看：

```scala
val f1 = Future {false}
val f2 = Future {2}
val f3 = Future {3}

f1.foreach { r1 =>
  if (r1) {
    f2.foreach { r2 =>
      println("### " + r2)
    }
  } else {
    f3.foreach { r3 =>
      println("### " + r3)
    }
  }
}
```

如果我们不考虑前面的`Await.result`带来的负面影响，使用它可以极大的简化代码：

```scala
val f1 = Future {false}
val f2 = Future {2}
val f3 = Future {3}

def await[T](future: Future[T]): T = Await.result(future, 1000 seconds)

if (await(f1)) {
  println("### " + await(f2))
} else {
  println("### " + await(f3))
}
```

我们能否找到一种鱼与熊掌兼得的方式？

async/await
-----------

自从scala提供了宏的功能以后，我们就可以实现各种奇葩的功能，包括这里要介绍的`async/await`:https://github.com/scala/async

这个库实现了C#中引入的`async/await`关键字（类似的还有Javascript将在ES7中提供的`async/await`），让我们可以写出看起来非常简洁的同步形式的代码，但实际执行却是异步的。

我们只需要在项目中添加这个依赖：

    "org.scala-lang.modules" %% "scala-async" % "0.9.2"

就可以将上面的代码写成这样：

```scala
import ExecutionContext.Implicits.global
import scala.async.Async.{async, await}

async {
  val f1 = async {false}
  val f2 = async {2}
  val f3 = async {3}

  if (await(f1)) {
    println("### " + await(f2))
  } else {
    println("### " + await(f3))
  }
}
```

看起来跟前面的`Await.result`的版本非常像，只是用`async`替换了`Future`，然后使用了它提供了`await`函数。

但是神奇之处在于，这段代码中的`await`是不会阻塞当前线程的！这段代码在编译期，会被转换成异步形式，依然是非阻塞的。（实际代码是用状态机，但可以按前面`future.foreach`那种多重嵌套的代码来理解）

使用这种方式，会有一些条件限制，详情可见其主页。但是对于多数情况，只要能使用就应该尽量使用，因为它可以大大提高代码的可读性。

基本实现原理
----------

在这份文档中，详细描述了它的实现原理：http://docs.scala-lang.org/sips/pending/async.html

文档在最后展示了下面这段代码： 

```scala
val future = async {                                     
  val f1 = async { true }                                 
  val x = 1                                               
  def inc(t: Int) = t + x                                 
  val t = 0                                               
  val f2 = async { 42 }                                   
  if (await(f1)) await(f2) else { val z = 1; inc(t + z) }
}
```

在编译器转换为scala代码后会是什么样子。因为里面的命名和结构有点乱，难以阅读，所以我对它进行了一些重构，如下：

```scala
object AsyncAwaitImpl {
  abstract class StateMachine[T, K]
  class MyStateMachine extends StateMachine[Promise[Int], ExecutionContext] {
    val f1 = Future {true}
    val x = 1
    def inc(t: Int) = t + x
    val t = 0
    var f2 = Future {42}

    var state = 0
    val resultPromise = Promise[Int]()
    var result: Int = 0

    var f1Result: Boolean = false
    var f2Result: Int = 0

    def resumeAsync(): Unit = try {
      state match {
        case 0 => {
          f1.onComplete(this.apply)
        }
        case 1 => {
          result = 0
          if (f1Result) {
            state = 2
            resumeAsync()
          } else {
            state = 3
            resumeAsync()
          }
        }
        case 2 => {
          f2.onComplete(this.apply)
        }
        case 5 => {
          result = f2Result
          state = 4
          resumeAsync()
        }
        case 3 => {
          result = {
            val z = 1
            inc(t + z)
          }
          state = 4
          resumeAsync()
        }
        case 4 => resultPromise.complete(Success(result))
      }
    } catch {
      case NonFatal(tr) => resultPromise.complete(Failure(tr))
    }

    def apply(result: Try[Any]): Unit = state match {
      case 0 => {
        if (result.isFailure) {
          resultPromise.complete(result.asInstanceOf[Try[Int]])
        } else {
          f1Result = result.get.asInstanceOf[Boolean]
          state = 1
          resumeAsync()
        }
      }
      case 2 => {
        if (result.isFailure) {
          resultPromise.complete(result.asInstanceOf[Try[Int]])
        } else {
          f2Result = result.get.asInstanceOf[Int]
          state = 5
          resumeAsync()
        }
      }
    }

    def apply(): Unit = resumeAsync()
  }

  val myStateMachine = new MyStateMachine()
  Future(myStateMachine())
  myStateMachine.resultPromise.future

}
```


这段代码的可读性已经比较好了，只需要细细阅读一遍就能清楚，这里只说一些重点：

1. 实现了一个状态机，对于不同的分支将进入不同的状态，这些状态在从开始到结束期间，将可能跨多个future
2. 对于`await(future)`，将会收集出哪些代码要使用`future`的值，然后把它们加到`future.onComplete`中，实现异步

在看`async/await`相关的文档时，虽然它们有各种各样详细的说明和介绍，但都没有明确的告诉读者，使用`async/await`后的代码，会不会阻塞当前线程。可能在一些地方隐约的透露了，但对于一个初学者来说，没有明确的说明，总让人不放心，不敢用。

好在后来我在[这个回答](http://stackoverflow.com/a/20339774/342235)中看到有人明确提到了，并且我自己也做了一些试验，这才放下心来。

关于Future中的`Await.result`以及`async/await`是否会阻塞当前代码的例子，我将会在另一篇文章中详细说明。

