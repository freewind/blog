---
layout: post
id: 2759
alias: how-to-define-a-simplest-hello-word-task-in-sbt
tags: sbt
date: 2014-09-12 22:57:23
title: 如何在sbt中定义一个最简单的hello world任务
---

使用`taskKey`函数，定义一个`TaskKey`类型的key，然后再给它赋一个函数即可。

## build.sbt

```scala
name := "hello"

version := "1.0"

scalaVersion := "2.11.0"

lazy val hello = taskKey[Unit]("An example task")

hello := { println("Hello, world") }
```

运行：

```
$ sbt hello
Hello, world
```

注意：

1. 中间的空行不能少
2. 通常使用`lazy`以避免某些初始化顺序问题

## show

另外，如果想看`hello`的返回值，使用`show`：

```
$ sbt
> show hello
[info] ()
```

## inspect

如果想看该任务的详细信息：

```
[info] Task: Unit
[info] Description:
[info]  demo task
[info] Provided by:
[info]  {file:/private/tmp/sbttest/}sbttest/*:hello
[info] Defined at:
[info]  /private/tmp/sbttest/build.sbt:9
[info] Delegates:
[info]  *:hello
[info]  {.}/*:hello
[info]  */*:hello
```