---
layout: post
id: 2779
alias: use-a-jar-not-inside-a-maven-repo-as-dependency-in-sbt
tags: sbt
date: 2014-09-13 13:40:38
title: 在sbt中使用一个不在maven库中的jar作为依赖
---

只上代码不解释：

```scala
libraryDependencies += "slinky" % "slinky" % "2.1" from "http://
   slinky2.googlecode.com/svn/artifacts/2.1/slinky.jar"
```