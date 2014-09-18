---
layout: post
id: 2780
alias: understand-the-output-of-sbt-inspect
tags: sbt
date: 2014-09-15 12:50:47
title: sbt中读懂inspect的输出
---

在sbt中，我们可以使用`inspect`命令查看一个key，比如一个任务，一个设置等。

（如果本文中有一些内容不理解，可以参看我写的关于sbt的其它文章)

## inspect name

先以最常见的`name`为例。我们都需要在`build.sbt`中定义一个name，表示所在的项目名称：

```scala
name := "my-project"
```

使用`inspect`来查看它：

```
$ sbt
> inspect name
[info] Setting: java.lang.String = my-project
[info] Description:
[info]  Project name.
[info] Provided by:
[info]  {file:/Users/freewind/workspace/sbt-scope-test/}sbt-scope-test/*:name
[info] Defined at:
[info]  /Users/freewind/workspace/sbt-scope-test/build.sbt:1
[info] Reverse dependencies:
[info]  *:onLoadMessage
[info]  *:description
[info]  *:normalizedName
[info]  *:projectInfo
[info] Delegates:
[info]  *:name
[info]  {.}/*:name
[info]  */*:name
```

如何看懂这些描述呢？

## Setting

```
[info] Setting: java.lang.String = my-project
[info] Description:
[info]  Project name.
```

表示`name`是一个值类型为`String`的Setting，其值为`my-project`，说明信息是`Project name`。

从这些内容我们可以反推出它的定义，应该形如：

```scala
lazy val name = settingKey[String]("Project name.")

name := "my-project"
```

###  Task

补充：除了`Setting`，还有`Task`类型的。比如我这样定义一个`hello`:

```scala
lazy val hello = taskKey[Unit]("hello task")

hello := println("hello")
```

则：

```
> inspect hello
[info] Task: Unit
[info] Description:
[info]  hello task
...
```

可以看到，这里显示的就是`Task: Unit`了。

## Provided by

然后是`Provided by`:

```
[info] Provided by:
[info]  {file:/Users/freewind/workspace/sbt-scope-test/}sbt-scope-test/*:name
```

这句长长的信息是在告诉我们，它在哪里找到了符合我们输入的`key`的完整定义。我们输入的命令是：

    inspect name

这里的`name`只给了key名，没有同时给出project/config/inkey等scope信息。根据sbt的定义，只给出名字时，这个key表示：

> 其所在项目的，以global为config，global为inkey的名为`name`的key

其中`inkey`表示在配置中定义的另一个key，可以是settingKey，taskKey和inputKey.

`inspect`会自动帮我们填上缺少的部分，然后寻找与其匹配的scope最接近的key，以`Provided by`的形式显示出来。

在匹配过程中，如果指定的project不存在，则去寻找`ThisBuild`，即整个build范围。如果指定的config或者inkey不存在，则去寻找`global` scope，直到找到最接近的一个。

比如：

    {file:/Users/freewind/workspace/sbt-scope-test/}sbt-scope-test/*:name

这句话，就是告诉我们，对于我们输入的`name`这个key，它找到的最接近的是：位于`file:/.../sbt-scope-test/`目录下的`sbt-scope-test`项目中的，以`global`为config的，名为`name`的key

### 输入更完整的key

我们还可以通过指定其它scope信息，来输入更完整的key，比如:

    inspect compile:name

根据前面所说，sbt在找不到精确匹配的情况下，会去寻找**最接近**的key。所以sbt会先去找有没有这样的定义：

    name in compile := ???

如果找不到，再去找:

    name in Global := ???

或者它的等价形式：

    name := ???

试一下：

```
$ sbt
> inspect compile:name
[info] Setting: java.lang.String = my-project
[info] Description:
[info]  Project name.
[info] Provided by:
[info]  {file:/Users/freewind/workspace/sbt-scope-test/}sbt-scope-test/*:name
...
```

可以看到，它没有找到精确匹配`compile:name`的key，最终找到的还是`*:name`，跟前面一样。

但是如果我们在`build.sbt`给它添加一下：

```scala
name in Compile := "my-project-for-compile"
```

注意，这里写的是`Compile`，它对应的是一个`config`，而不是`compile` task.

则：
```
> inspect compile:name
[info] Setting: java.lang.String = my-project-for-compile
[info] Description:
[info]  Project name.
[info] Provided by:
[info]  {file:/Users/freewind/workspace/sbt-scope-test/}sbt-scope-test/compile:name
[info] Defined at:
[info]  /Users/freewind/workspace/sbt-scope-test/build.sbt:7
[info] Delegates:
[info]  compile:name
[info]  *:name
[info]  {.}/compile:name
[info]  {.}/*:name
[info]  */compile:name
[info]  */*:name
[info] Related:
[info]  *:name
```

可以看到，此时的`Provided by`就已经变成：

    {file:/Users/freewind/workspace/sbt-scope-test/}sbt-scope-test/compile:name

而不是`*:name`了，说明它找到了更精确的key。

## Defined at

接着是`Defined at`:

```
[info] Defined at:
[info]  /Users/freewind/workspace/sbt-scope-test/build.sbt:1
```

它很有用，直接告诉我们这个key是在哪个文件的哪一行定义的，很酷，方便我们去寻找定义。

## Dependencies

这个`Dependencies`是说这个key依赖于别的哪些key。因为我们在例子中使用的`name`并不依赖于别的key，所以没有显示该节。在这一节我将换个例子：

```scala
lazy val myname = settingKey[String]("a setting of myname")

myname <<= name { n => n + "!!!"}
```

我们定义了一个`myname`的key，它依赖于key `name`的值。

```
$ sbt
> inspect myname
...
[info] Dependencies:
[info]  *:name
```

这里会显示`Dependencies`项，告诉我们这个key依赖另一个定义config scope为`global`的名为`name`的key.

## Reverse dependencies

接下来是`inspect name`中的`Reverse dependencies`了。它表示其它哪些key依赖于本key:

```
[info] Reverse dependencies:
[info]  *:onLoadMessage
[info]  *:description
[info]  *:normalizedName
[info]  *:projectInfo
```

## Delegates

前面说，当我们输入了一个key后，sbt会自动匹配跟它最接近的key。在匹配的过程中，是有一条由窄到宽的路径的，而`Delegates`就是告诉我们这条路径究竟是什么样的。

对于我们的例子`inspect name`来说，它的`Delegates`是这样的：

```
[info] Delegates:
[info]  *:name
[info]  {.}/*:name
[info]  */*:name
```

对于`name`来说，它的**由窄到宽**的路径是这样的：

    name -> *:name -> {.}/*:name -> */*:name

其中`{.}/*:name`中的`{.}/`表示整个build(`ThisBuild`)，`*/*:name`中的`*/`表示global project。我觉得它们两者之间很相似，但搜索顺序不同。

晚点会专门写一篇文章来讲`ThisBuild`和global project之间的区别，这里只要先知道它们的顺序就行了。

## Related

`inspect`还有一个`Related`，在`inspect name`的例子中没有体现，这里换成`inspect compile:name`.

首先是`build.sbt`:

```scala
lazy val myname = settingKey[String]("a setting of myname")

myname in Test := "my-name-in-test"
```

然后运行：

```
$ sbt
> inspect compile:myname
[info] No entry for key.
[info] Description:
[info]  a setting of myname
[info] Delegates:
[info]  compile:myname
[info]  *:myname
[info]  {.}/compile:myname
[info]  {.}/*:myname
[info]  */compile:myname
[info]  */*:myname
[info] Related:
[info]  test:myname
```

可以看到，虽然它没有找到`compile:myname`的值，但是它发现`myname`在其它的scope中有值，便把它们显示在`Related`里。

