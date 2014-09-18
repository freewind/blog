---
layout: post
id: 2772
alias: hello-world-in-scala
tags: scala
date: 2014-09-13 15:34:44
title: Hello world in scala
---

```scala
object Hello extends App {
  println("Hello, world!")
}
```

或者

```scala
object Hello {
  def main(args: Array[String]) {
    println("Hello, world!")
  }
}
```