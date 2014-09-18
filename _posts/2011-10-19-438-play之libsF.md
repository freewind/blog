---
layout: post
id: 438
alias: play-libs-f
tags: PlayFramework1
date: 2011-10-19 13:18:39
title: play之libs.F
---

我是在看play自带的聊天室程序`samples-and-tests/chat`时，发现了这个类`libs.F`。

Play的文档，在基本使用方面写得很详细，但对于较高级的功能，如这个`libs.F`类，则非常简略。没有注释，没有文档，只有源代码，以及网上一篇辛苦找到的很简单的介绍性文章：[https://github.com/playframework/play/blob/master/documentation/manual/libs.textile](https://github.com/playframework/play/blob/master/documentation/manual/libs.textile)

这里做一下简单介绍（边学边写）：

libs.F中的F，指的是&#8221;Functional programming&#8221;，即函数式编程。Java本身不支持函数式编程，所以play试图将一些函数式编程中的概念移植到java中，所以有了这个libs.F。

libs.F虽然是一个类，但play其实是把它当成&#8221;包&#8221;在用。因为里面定义了很多public类和方法，放在一起显得更加方便。

 <span id="more-438"></span>
<p>基本结构如下：

![](/user_images/438-0.png)

虽然定义了不少类，但是如果你对scala有所了解的话，可以看出它实际上模拟了scala中的一些概念。

**1. Option**

Option<T>, None<T>, Some<T>三个类，与scala中的Option一一对应，其作用也是一样的：避免null的使用和NullPointerException的产生。更详细的讲解可参看scala中的相关文章。

Some和None都继承于Option类，提供了`isDefined`和`get`等方法。所以以前这样写：

```
if (obj==null) {
    // do something
} else {
    // do something else
}
```

现在则写成：

```
if (opt.isDefined()) {
    // do something
} else {
    // do something else
}
```

错！！如果仅仅是这样，那么没有任何意义。使用Option就是为了让我们省掉这个判断，直接使用其值（如果有的话）。

在Scala中，我们可以这样做：

```
opt map { v => dosomething(v) }
```

在java中怎么模拟呢？使用`for`。

None和Some类都实现了Iterable接口，这意味着我们可以使用这种用法：

```
for ( T v : opt ) {
    dosomething(v);
}
```

如果对象opt为None，则循环中的内容不执行；否则执行一次。Play通过这种巧妙的方式，避免了判断，模拟出了scala中的效果。

**2. Tuple**

同样模仿了Scala，表示同时提供多个不同类型的值。不过这里只提供了最多5个元素（Scala中是22个），对应的类分别是Tuple, T2, T3, T4, T5。

**3. Either**

模仿了Scala，表示只提供了多种可能值中的一个。只提供了最多5种可能，对应的类是：Either, E2, E3, E4, E5。

**4. Pattern match**

模式匹配是Scala中的一个强大的特性，play在这里通过Matcher等类进行模仿。先看Scala中的例子：

```
obj match {
    case s:String if s.startsWith("command:") => println(s.toUpperCase())
    case _ => 
}
```

在Java中，应该写成这样：

```
if(obj instanceof String && ((String)obj).startsWith("command:")) {
    String s = (String)obj;
    System.out.println("s.toUpperCase());
}
```

而使用libs.F，可写成这样（注意其中中String是F.Matcher中定义的一个字段）：

```
static import libs.F.Matcher.*;
for (String s : String.and(StartsWith("command:")).match(obj)) {
    System.out.println(s.otUpperCase());
}
```

**5. 并发及异步**

上面的这些，在代码中使用的并不多。它们的存在，实际上是为了这里的&#8221;并发及异步&#8221;。

这里涉及到的类（和接口）有：Action, Action0, Promise, Timeout, EventStream, IndexedEvent, ArchivedEventStream

这一块的代码写得极其复杂且完全没有注释，看起来让人头大无比。这也能代表play整体的代码风格：代码写得比较玄，有些地方逻辑复杂，很难理解。

在play的google group中有一段描述，也许可以帮助理解：

> <pre>
> -- Promises: 
> In Play 1.2 we introduce Promise that is the Play's custom Future type 
> (Promise was named Task in previous commits on master) . In fact a 
> Promise<T> is also a Future<T> so you can use it as a standard Future. 
> But it has also a very interesting property: the ability to register 
> callback using onRedeem(…) that will be called as soon as the promised 
> value is available. It allows the framework to register itself on them 
> and to reschedule the request invocation as soon as possible. 
> Promises are used everywhere in Play in place of Future (for Jobs, 
> WS.async, etc…) 
> Promises can be combined in several way. For example: 
> - Promise p = Promise.waitAll(p1, p2, p3) 
> - Promise p = Promise.waitAny(p1, p2, p3) 
> - Promise p = Promise.waitEither(p1, p2, p3) 
> 
> -- play.libs.F: 
> The Promise type is part of new library (play.libs.F) that introduces 
> several useful constructs coming from functional programming. These 
> constructs are used to handle complex cases that I will expose just 
> later. For those that are accustomed to functional programming we 
> provide: 
> - Option<T> (a T value that can be or not set) 
> - Either<A,B> (contains either a A value or a B value) 
> - Tuple<A,B> (contains both A and B values) 
> 
> -- await(…): 
> So, now when your application code returns values that are not yet 
> available using a Promise<T>, you want to be able to ask Play to wait 
> for this promised result to be available before resuming your request. 
> It allows your code to say explicitly: "I'm waiting for a result that 
> will be available later", and so the framework to handle it as "Ok I 
> will stop your code, reuse the thread to serve other requests, and 
> resume your code as soon as the promised value you wait for is 
> available". 
> It is the point of the await(…) controller methods: 
> Promise<String> delayedResult = veryLongComputation(…); 
> await(delayedResult); 
> 
> -- Continuations: 
> Because the framework needs to recover the thread you were using in 
> order to use it to serve other requests, it has to suspend your code. 
> In the previous Play version the await(…) equivalent was waitFor(…). 
> And waitFor was suspending your action, and then recalling it later 
> from the beginning. 
> To make it easier to deal with asynchronous code in Play 1.2 we have 
> introduced continuations. Continuations allow your code to be 
> suspended and resumed transparently. So you write your code in a very 
> imperative way, as: 
> public static void computeSomething() { 
>      Promise<String> delayedResult = veryLongComputation(…); 
>      String result = await(delayedResult); 
>      render(result); 
> } 
> 
> In fact here, your code will be executed in 2 steps, in 2 different 
> threads. But as you see it, it's very transparent for your application 
> code. 
> Using await(…) and continuations, you could write a loop: 
> public static void loopWithoutBlocking() { 
>      for(int i=0; i<10; i++) { 
>           System.out.println(i); 
>           await("1s"); 
>      } 
>      renderText("Loop finished"); 
> } 
> 
> And using only 1 thread (which is the default in development mode) to 
> process requests, Play 1.2 will be able to run concurrently these 
> loops for several requests at the same time.
> ```

可以将Promise看作是jdk中提供的Future，可用于将某些任务异步执行。实际上Promise实现了Future，并且提供了一些增强的功能。作者在论坛的一个帖子里说，jdk提供的Future没什么用，所以才弄了一个Promise出来。

我们在代码中，使用的时候，通常是在controller中通过`await()`方法，将某一个耗时的操作暂停，将线程让出执行别的操作。等到耗时操作完成的时候，再回来继续刚才的操作。这样可以节省线程资源。

这里涉及的代码有点复杂，而且我也没有实际用过，所以先略过不提。等在使用中有了新的发现，再补充在这里。
