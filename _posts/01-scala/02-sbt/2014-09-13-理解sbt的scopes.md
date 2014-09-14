---
layout: post
id: 2767
alias: understand-sbt-scopes
tags: sbt
date: 2014-09-13 13:11:54
title: 理解sbt的scopes
---

这是sbt中比较难以理解的一部分，但是很重要，如果不能理解key与scope和value之间的关系，会遇到各种各样的障碍。

## key与value

在sbt中，它已经预定义了各种各样的key，比如:

** settings **

    name
    version

** tasks **

    compile
    test
    run

我们也可以自己定义一些key，比如在`build.sbt`中：

    lazy val myname = settingKey[String]("myname setting")

    lazy val hello = taskKey[String]("a hello task")

我们可以给这些key赋值，比如:

```scala
name := "sbt-scope-test"

version := "1.0.0"

hello := println("Hello world")
```

这种做法我们经常用到。

看起来每个key似乎都是和一个值相对应的。其实不是的。

## scope

一个key实际上是可以跟多个value对应的，它们之间是一对多的关系。这是因为每个key的背后，实际上存在着多个不同维度的scope，一个key跟相应的scope一起，才能和一个value一一对应。

目前有三种scope，分别是`project`, `config`, `task`。

假设我们有一个名为`keys`的map，用来保存key与value的值。当我们给一个key赋值时，它并不是：

    keys("mykey") = "some-value"

而是：

    keys("mykey", project, config, task) = "some-value"

`key名`与`project`、`config`和`task`这四个列合在一起才能加个“唯一索引”，才能跟某个值一一对应。

由于`project`、`config`和`task`都有默认值，所以我们有时候才能简写为：

    mykey := "some-value"

因为它实际上对应的是：

    keys("mykey", "self-project", "global-config", "global-task") = "some-value"

其中：

1. **self-project**: 包含这个key定义的`build.sbt`或者`Build.scala`所在的项目
2. **global-config**: 可匹配任意一个config
3. **global-task**: 可匹配任意一个task

关于`project`、`config`和`task`的意思，将在下面说明。

## project

在sbt项目中，我们可以简单的不定义额外项目，也可以定义多个子项目。每个项目都是一个project，可在给key赋值的时候引用。

按project给同一个key赋不同的值，这种需求是很常见的。比如对于key `name`是用来表示项目名的，它在不同的项目中，当然要有不同的值。

### 如果没有定义额外项目

如果我们没有定义额外项目，那我们就已经有了一个项目。比如当前项目是`sbt-scope-test`，它的`build.sbt`如下：

```scala
name := "myproject"

version := "1.0.0"
```

我们可以通过`projects`命令查看：

```
$ sbt
> projects
[info] In file:/Users/freewind/workspace/sbt-scope-test/
[info]   * sbt-scope-test
```

可以看到，它包含了唯一的一个项目，以目录名为项目名。

### 显示定义项目

我们可以给这个唯一的项目一个名字，这样可以对它进行一些操作（如`enablePlugins`)等。

**build.sbt**

```scala
name := "sbt-scope-test"

version := "1.0.0"

lazy val root = project in file(".")
```

再查看一下：

```
$ sbt
> projects
[info] In file:/Users/freewind/workspace/sbt-scope-test/
[info]   * root
```

可以看到还是一个项目，但是名字变了。

### 定义多个项目

我们还可以使用同样的语法定义多个项目：

```scala
name := "sbt-scope-test"

version := "1.0.0"

lazy val root = project in file(".")

lazy val core = project

lazy val util = project
```

这样又定义了两个额外的项目`core`和`util`，它们将以目录`myproject/core`和`myproject/util`作为它们的主目录。

查看：

```scala
$ sbt
> projects
[info] In file:/Users/freewind/workspace/myproject/
[info]     core
[info]   * root
[info]     util
```

可以看到现在有了三个project，当前选定的project是`root`

### 针对不同project赋值

我们可以针对不同的project，给同一个key赋不同的值：

```scala
lazy val myname = settingKey[String]("setting of myname")

myname in root := "my-root-name"

myname in util := "my-util-name"

myname in core := "my-core-name"
```

可以看到，我在不同的project中，给同一个key `myname`设置了不同的值。

查看：

```
$ sbt
> project
[info] root (in build file:/Users/freewind/workspace/sbt-scope-test/)
> myname
[info] my-root-name
>
> project core
[info] Set current project to core (in build file:/Users/freewind/workspace/sbt-scope-test/)
> myname
[info] my-core-name
>
> project util
[info] Set current project to util (in build file:/Users/freewind/workspace/sbt-scope-test/)
> myname
[info] my-util-name
```

可以看到，当我使用`project`命令切换到不同的project后，显示`myname`的值会不同。

再次提示：

    myname in root := "my-root-name"

它实际上相当于：

    keys("myname", "root", global-config, global-task) = "my-root-name"

## config

config实际上可以看作一些资源的集合。比如sbt提供了`compile`、`test`和`runtime`这几个config，它们包含的文件路径是不一样的。

### 如何定义config

```scala
lazy val myconf1 = config("myconf1")

lazy val myconf2 = config("myconf2")
```

sbt提供的`compile`、`test`和`runtime`等，也是同样的方式定义的，参看sbt的`Configuration.scala`:

```scala
lazy val Default = config("default")
lazy val Compile = config("compile")
lazy val IntegrationTest = config("it") extend(Runtime)
lazy val Provided = config("provided") ;
lazy val Docs = config("docs")
lazy val Runtime = config("runtime") extend(Compile)
lazy val Test = config("test") extend(Runtime)
lazy val Sources = config("sources")
lazy val System = config("system")
lazy val Optional = config("optional")
lazy val Pom = config("pom")
```

### 赋值时指定config

```scala
myname in myconf1 := "my-conf1-name"

myname in myconf2 := "my-conf2-name"
```

查看：

```
$ sbt
> myconf1:myname
[info] my-conf1-name
> myconf2:mynname
[info] my-conf2-name
```

可以看到，需要通过`config:key`的方式来指定显示哪个config下的key。

注意：两者之间是以`:`分隔的，这种写法是固定的。也就是说，每当你看到形如`a:b`的名称时，你就可以推断`a`是一个config，`b`是一个key或者task

(后面还会介绍由`::`分隔的写法，代表不同的意思)

## task

task就是我们可以执行的一些操作，比如`compile`,`test`,`run`等。

因为同一个key（比如一个设置）在不同的task中，可能有不同的值，所以在给key赋值时，也可以考虑按task来分。

### 定义一个task

我们可以通过下面的方式定义一个task:

```scala
lazy val hello = taskKey[Unit]("a hello task")

hello := println("Hello, world")
```

执行：

```
$ sbt
> hello
Hello, world
```

### 按task给key赋值

假设我定义了两个task和一个key，并且这两个task都依赖于这个key:

```scala
lazy val myname = settingKey[String]("my name")

lazy val upper = taskKey[Unit]("print upper name")

lazy val lower = taskKey[Unit]("print lower name")

upper <<= (myname in upper) map { n => println("Hello, " + n.toUpperCase) }

lower <<= (myname in lower) map { n => println("Hello, " + n.toLowerCase) }
```

可以看到`upper`将打印出`myname`的大写，而`lower`打印出小写。在上面的代码中，我们还没有给`myname`赋值，但是`myname`作为一个占位符，是可以使用的。

```scala
myname in upper := "Jeff"

myname in lower := "Lily"
```

### 运行

```
$ sbt
> upper
Hello, JEFF
> lower
Hello, lily
```

### 查看myname

```
$ sbt
> upper::myname
[info] Jeff
> lower::myname
[info] Lily
```

注意其格式为`task::key`，即两者之间是用`::`分隔的。这种格式是固定的，每当我们看到形如`a::b`的命名时，我们就可以推断出`a`是一个task（或者也是一个key），而`b`是另一个key（或者task)

如果和前面的config结合起来，其格式为：

    config:task::key

注意第一个分隔符是`:`，第二个是`::`

## global是安全网

在我们给一个key赋值时，如果我们显式指定了某一个`config`或者`task`的时候，它会跟相应的config和task绑定起来。如果没有指定，则会采用默认值`Global`。

当然，我们也可以显式指定为`Global`，如：

    myname in Global := "Global name"

其等价于：

    myname := "Global name"

### 如果找到了指定的config

假设我指定了某个config:

```scala
myname in myconf1 := "my name in conf1"
```

当我查看`myconf1:myname`时：

```
$ sbt
> myconf1:myname
[info] my name in conf1
```

它会显示出正确的值。

### 如果找不到指定的config，会找global

但如果我没有上面的`myname in myconf1 := ...`的定义，它则会找到`global:myname`，打印出：

    [info] Global name

由此可见，我们可以把Global当作安全网，定义一些比较通用的默认值。这样，当我们执行与某个config特定的任务但又找不到相应的值的时候，还可以用global的值来顶一下。

## key的完整形式

根据前面的说明，一个key实际上要跟`project`,`config`,`task`等绑定在一起的。那么我们需要制定一个格式，即可以让我们精确指定，又可以在显示时准确显示。

它的格式如下：

    {<build-uri>}<project-id>/config:inkey::key

### 举个例子

来一个完整的例子：

```scala
lazy val root = project in file(".")

lazy val myconf = config("myconf")

lazy val hello = taskKey[Unit]("hello task")

lazy val myname = settingKey[String]("a setting of myname")

myname in(root, myconf, hello) := "my complete name"
```

对于这个`myname`，我们要想引用它，必须写成下面的形式，否则找不到：

    root/myconf:hello::myname

`inspect`一下看看：

```
$ sbt
> inspect root/myconf:hello::myname
[info] Setting: java.lang.String = my complete name
[info] Description:
[info]  a setting of myname
[info] Provided by:
[info]  {file:/Users/freewind/workspace/sbt-scope-test/}root/myconf:hello::myname
[info] Defined at:
[info]  /Users/freewind/workspace/sbt-scope-test/build.sbt:20
[info] Delegates:
[info]  root/myconf:hello::myname
[info]  root/myconf:myname
[info]  root/*:hello::myname
[info]  root/*:myname
[info]  {.}/myconf:hello::myname
[info]  {.}/myconf:myname
[info]  {.}/*:hello::myname
[info]  {.}/*:myname
[info]  */myconf:hello::myname
[info]  */myconf:myname
[info]  */*:hello::myname
[info]  */*:myname
```

(如果当前正处于`root` project，则也可以写成：`inspect myconf:hello::myname`，省掉前面的项目名)

从`Provided by:`一节，可以看到它的完整形式如下：

```
{file:/Users/freewind/workspace/sbt-scope-test/}root/myconf:hello::myname
```

对比前面的定义：

    {<build-uri>}<project-id>/config:inkey::key

其中：

1. `{<build-uri>}`: 项目`root`所在的目录`{file:/Users/freewind/workspace/sbt-scope-test/}`
2. `<project-id>`: 该key所属的项目名`root`
3. `config`: 该key所属的config `myconf`
4. `inkey`: 该key所属的task `hello`
5. `key`: 该key的名称`myname`

这种格式在我们`inspect`某个key时非常常见，我们一定要记住。

关于如何读懂`inspect`的结果，将会写在另一篇博客里。

