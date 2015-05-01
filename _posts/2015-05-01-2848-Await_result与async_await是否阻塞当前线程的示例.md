---
layout: post
id: 2848
alias: examples-to-check-if-await-result-and-async-await-will-block-current-thread
tags: future async await
date: 2015-05-01 19:01:01
title: Await.result与async/await是否阻塞当前线程的示例
---

在另一篇文章中提到，`Await.result`会阻塞当前线程，而`async/await`不会。虽然我们从实现原理上有一些解释，但是没有可运行的实例代码，还是觉得不够放心。这里将会以一些代码实例来说明。

Await.result
------------

### 线程数为1的线程池

`Await.result(future, ...)`会阻塞当前线程。下面我将设计一段代码来证明它：

```scala
package myawait

import java.util.concurrent.Executors

import scala.concurrent.duration._
import scala.concurrent.{Await, ExecutionContext, Future}
import scala.language.postfixOps
import scala.util.{Failure, Success}

object MyAwaitResult extends App {

  implicit val ec = ExecutionContext.fromExecutor(Executors.newFixedThreadPool(1))

  val allStart = System.currentTimeMillis()

  log("start")

  val future = Future {
    val f1 = Future {log("f1 running"); true}
    val f2 = Future {log("f2 running: "); 42}
    if (Await.result(f1, 3 seconds)) {
      Await.result(f2, 3 seconds)
    } else {
      log("oh, no")
    }
  }

  log("started future")

  future.onComplete {
    case Success(v) => log("value: " + v)
    case Failure(e) => log(e.toString)
  }

  def log(str: String) = {
    println("### " + str + ", " + (System.currentTimeMillis() - allStart) + "ms, " + Thread.currentThread().getName)
  }

}
```

运行结果如下： 

```
### start, 81ms, main
### started future, 125ms, main
### f1 running, 3151ms, pool-1-thread-1
### f2 running: , 3152ms, pool-1-thread-1
### java.util.concurrent.TimeoutException: Futures timed out after [3 seconds], 3152ms, pool-1-thread-1
```

可见它并没有打印出`value: 42`，而是抛出了一个`Futures timed out after [3 seconds]`的异常。

原因是，当我们创建了一个线程数为1的ExecutionContext并设为`implicit`后，后面的`Future {}`都会把新建的任务追加到这唯一的线程中。当创建`future`后，里面的代码就占据了第一个任务中。在这个任务中创建的`f1`和`f2`，都会追加到这个线程中，变成第2个和第3个任务。如果第1个任务不执行完，它们是没有机会执行的。

但是在第一个任务中，有`Await.result(f1, 3 seconds)`，它在等待这个没有机会执行的`f1`完成，所以只会超时抛出异常。之后`f1`和`f2`才有机会得到执行（可以看到它们的执行时间都在3秒以后），但期待中的`future`的值`42`就再也没有机会打印出来了。

### 线程数为2的线程池

如果我们仅仅把上面代码的线程数变成`2`，它的结果就不一样了。

```scala
implicit val ec = ExecutionContext.fromExecutor(Executors.newFixedThreadPool(2))
```

将打印出结果：

```
### start, 79ms, main
### started future, 117ms, main
### f1 running, 118ms, pool-1-thread-2
### f2 running: , 120ms, pool-1-thread-2
### value: 42, 134ms, pool-1-thread-1
```

可见由于多了一条线程，`f1`和`f2`有机会快速执行，所以`Await.result(f1, 3 seconds)`就可以及时的拿到值，打印出最终结果。

由些可以证明，`Await.result`的确会阻塞当前线程。如果我们在生产代码中使用了它，很有可能很快把我们的线程池占光，导致其它任务没有机会执行。

async/await
-----------

如果我们用`async/await`改写之后，看看会怎么样。依然保持池子的线程数为1：

```scala
package myawait

import java.util.concurrent.Executors

import scala.async.Async.{async, await}
import scala.concurrent.ExecutionContext
import scala.language.postfixOps
import scala.util.{Failure, Success}

object MyAsyncAwait extends App {

  implicit val ec = ExecutionContext.fromExecutor(Executors.newFixedThreadPool(1))

  val allStart = System.currentTimeMillis()

  log("start")

  val future = async {
    val f1 = async {log("f1 running"); true}
    val f2 = async {log("f2 running: "); 42}
    if (await(f1)) {
      await(f2)
    } else {
      log("oh, no")
    }
  }

  log("started future")

  future.onComplete {
    case Success(v) => log("value: " + v)
    case Failure(e) => log(e.toString)
  }

  def log(str: String) = {
    println("### " + str + ", " + (System.currentTimeMillis() - allStart) + "ms, " + Thread.currentThread().getName)
  }

}
```

执行后打印结果：

```
### start, 83ms, main
### started future, 123ms, main
### f1 running, 125ms, pool-1-thread-1
### f2 running: , 125ms, pool-1-thread-1
### value: 42, 125ms, pool-1-thread-1
```

可以看到，就算只有一个线程，依然很快的打印出了结果，并没有阻塞。由此可以证明，`Await.result`的确会阻塞线程，而`async/await`不会，这样我们就可以放心大胆的使用`async/await`了。

Await.result的实现原理
--------------------

`Await.result`方法的实现如下：

```scala
def result[T](awaitable: Awaitable[T], atMost: Duration): T =
      blocking(awaitable.result(atMost)(AwaitPermission))
```

它里面实际上有两个地方，可以用来等待结果及阻塞当前线程：

1. `awaitable.result(...)`
2. `blocking(...)`

对于前面使用的例子来说，实际上起作用的是`awaitable.result(...)`

### `awaitable.result(future, atMost)`

大致作法是创建了一个`CompletionLatch`锁，这个锁默认是锁上的，直到`future`完成时，才给它解锁。而当前线程，就会在`atMost`时间内，尝试拿锁，因为拿不到，所以就阻塞起来，直到超时或者拿到锁。

在阻塞前，其实也进行了几种不同的尝试。首先尝试拿一下；如果拿不到，则看看等待的剩余时间是否极短（小于1000纳秒），如果是的话，则用`while(true)`轮询，会消耗cpu的；否则使用`LockSupport.park`方法阻塞当前线程（类似于`obj.wait()`），不消耗CPU，等待其它线程调用`LockSupport.unpark`（类似于`obj.notify()`）把它唤醒。

### `blocking(...)`

`blocking`的实现如下：

```scala
def blocking[T](body: =>T): T = BlockContext.current.blockOn(body)(scala.concurrent.AwaitPermission)
```

`BlockContext.current`实现如下：

```scala
def current: BlockContext = contextLocal.get match {
    case null => Thread.currentThread match {
      case ctx: BlockContext => ctx
      case _ => DefaultBlockContext
    }
    case some => some
  }
```

而`DefaultBlockContext`实现如下：

```scala
private object DefaultBlockContext extends BlockContext {
    override def blockOn[T](thunk: =>T)(implicit permission: CanAwait): T = thunk
  }
```

也就是说，默认情况下，如果某个任务在普通的线程中执行，这个`blocking`将走到`DefaultBlockContext.blockOn`，而它实际上什么也没做。在前面的例子中，就是这么走的，所以对于此例来说，真正起作用的还是`awaitable.result(...)`.

但是，如果我们的任务是运行在一个实现了`BlockContext`的特殊线程中，那么这个`blocking`就会起作用。比如在Scala中的某处，就有一个用`ForkJoinPool`实现的`blocking`。

ForkJoinPool
------------

这个东西也值得一看：

1. <http://www.javabeat.net/simple-introduction-to-fork-join-framework-in-java-7/>
2. <http://stackoverflow.com/questions/29988713/difference-between-forkjoinpool-and-normal-executionservice/29988883>









