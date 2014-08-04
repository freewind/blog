---
layout: post
title: "为什么Nil的类型是List[Nothing]，而不是List[Any]"
tags:
  - scala-hot-workshop
date: 2014-07-23 21:50:24
---

今天在做scala workshop讲到Nil的类型时，杜娟提了一个很好的问题：为什么Nil的类型是List[Nothing]，而不是List[Any]

首先看Nil的定义：

    case object Nil extends List[Nothing] {
      ..
    }
    

    对于Scala的List来说，它是不可变的，不会往里面添加东西。这样的话，它的类型是List[Nothing]还是List[Any]似乎都没什么区别才对。

    让我们从类型系统方面考虑一下，很快就能知道原因。

    我们定义一个List的时候，可以使用这样的方式：

    val list = 1 :: 2 :: Nil
    

    它实际上等于：

    val list = Nil.::(2).::(1)
    

    让我们看一下`::`的定义（简化了）：

    class List[+A] {
      def ::[B >: A] (x: B): List[B] =
        new scala.collection.immutable.::(x, this)
    }
    

    再看一下`immutable.::`的定义：

    case class ::[B](hd: B, tl: List[B])
    

    可见`::[B]`中B的类型，将由传入的两个参数`hd`和`tl`的公共父类决定。如果Nil继承自List[Any]，那么：

    Nil.::(1)
    

    即：

    ::(1, Nil)

的类型，将是`Int`和`Any`的父类，即`Any`。这样的话，我们不论怎么写，都只能得到List[Any]类型的List，显然是没有意义的。

但反过来，如果Nil继承自List[Nothing]，由于Nothing是任意类型的子类，所以它与另一个类型的公共父类型总是另一个类型，所以我们就可以轻易由另一个类型来确定List的类型了。