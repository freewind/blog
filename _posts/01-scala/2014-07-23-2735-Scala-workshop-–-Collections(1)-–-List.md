---
layout: post
id: 2735
alias: scala-workshop-collections-1-list
tags: scala-hot-workshop
date: 2014-07-23 17:45:36
title: Scala workshop – Collections(1) – List
---

* * *

layout: reveal

## title: Scala Workshop &#8212; Collections(1)

# Collection

List

// Set/Map & Tuple/Option & Array

* * *

## Scala为什么重写集合库

1.  Java中已经提供了一套强大的集合库，比如List/Set/Map等等
2.  JVM上一些其它的面向对象语言，如Groovy/Xtend/Kotlin等等，都是直接或间接使用Java的集合类库
3.  但Scala不但不用，还自己重写了两套：一套&#8221;不可变&#8221;的，一套&#8221;可变的&#8221;

* * *

### Scala为什么要&#8221;重新造轮子&#8221;？

* * *

## 三个原因

1.  Scala最初希望同时运行于Java和.net平台，所以需要尽量保持独立，不要与Java的类库产生太多交集
2.  Scala是一门面向对象+函数式的语言，而函数式语言追求&#8221;不可变的数据类型&#8221;，而Java的类库都是可变的
3.  Scala的类型系统与Java不同

    1.  Java中的集合类不支持协变/逆变
    2.  Scala早期甚至不支持Java的泛型（后来加入&#8221;存在类型&#8221;）

* * *

## 引申小知识

1.  Scala.net 被官方废弃 [https://github.com/magarciaEPFL/scaladotnet](https://github.com/magarciaEPFL/scaladotnet)
2.  但IKVM ([http://ikvm.net](http://ikvm.net)) 在.net上实现了一套JVM，可以运行Java程序
3.  scala.js 正在蓬勃发展中 [http://lihaoyi.github.io/roll/](http://lihaoyi.github.io/roll/)

* * *

### Scala同时提供了两套集合库:

1.  不可变：`scala.collection.immutable`
2.  可变： `scala.collection.mutable`

* * *

## 我们通常都应该使用&#8221;**不可变**&#8220;的

* * *

## scala._

    package object scala {
      type List[+A] = scala.collection.immutable.List[A]
      val List = scala.collection.immutable.List

      val Nil = scala.collection.immutable.Nil

      type ::[A] = scala.collection.immutable.::[A]
      val :: = scala.collection.immutable.::
    }
    

    * * *

    ## List api快速浏览

    * * *

    ## 创建普通List：

    val list = List("aaa","bbb","ccc")
    val list2 = List(1,2,3,4,5,6,7)
    

    * * *

    其它方式：

    List.range(1, 5)  // List(1,2,3,4,5)
    (1 to 5).toList   //  List(1,2,3,4,5)
    List.fill(5)('a')  // List('a', 'a', 'a', 'a', 'a')
    

    * * *

    ## 创建空List:

    val list = List()
    val list = List[String]()
    val list2 = List.empty[String]
    Nil
    

    * * *

    ## 基本操作

    val list = List(1,2,3,4)

    list.head // 1
    list.headOption // Some(1)
    list.tail // List(2,3,4)
    list.init // List(1,2,3)
    list.last // 4
    list.lastOption // Some(4)

    list(0) // 1
    list.apply(0) // 1
    list.length // 4
    list.size // 4
    list.isEmpty // false
    list.reverse // List(4,3,2,1)
    

    * * *

    ## 更多操作

    val list = List(5,6,7,8)

    list.drop(2)      // List(7,8)
    list.take(3)       // List(5,6,7)
    list.splitAt(2)   // (List(5,6),List(7,8))
    list.indices      // Range(0, 1, 2, 3)
    list.takeWhile (_ < 7)    // List(5,6)
    list.dropWhile (_%2==1)   // List(6, 7,8)
    

    * * *

    &nbsp;

    1 :: List(2,3,4)
    1 :: 2 :: 3 :: 4 :: Nil

    List(1,2,3) :+ 4 
    List(1,2) ::: List(3,4)

    List(1,2,3).mkString(",") // 1,2,3
    List(1,2,3).mkString("[",",","]") // [1,2,3]
    

    * * *

    &nbsp;

    List(5,6,7).zip(List(0,1,2))   // List((5,0), (6,1), (7,2))
    List((5,0), (6,1), (7,2)).unzip   // (List(5, 6, 7),List(0, 1, 2))
    List(5,6,7).zipWithIndex    // List((5,0), (6,1), (7,2))
    

    * * *

    &nbsp;

    List(1, 2, 3) map (_ + 1)
    List(1, 2, 3) map (x => x + 1)
    List(1,2,3).map(x => List(x,x))   // List(List(1,1), List(2,2), List(3,3))
    List(List(5,6), List(7,8)).flatten() // List(5,6,7,8)
    List(1,2,3).map(x => List(x,x)).flatten   // List(1,1,2,2,3,3)
    List(1,2,3).flatMap(x => List(x,x))    // List(1,1,2,2,3,3)
    

    * * *

    &nbsp;

    List(1,2,3) foreach { num => println("Num is: " + num) }
    List(1, 2, 3, 4, 5) filter (_ % 2 == 0)          // List(2, 4)
    List(1, 2, 3, 4, 5) partition (_ % 2 == 0)    // (List(2, 4),List(1, 3, 5))
    List(1,2,3,4,5) find (_%2==0)             // Some(2)
    List(1, 2, 3, 4, 5) find (_ <= 0)             // None
    List(1, 2, 3, -4, 5) span (_ > 0)  // (List(1, 2, 3),List(-4, 5))
    List(1,2,3,4,5,6).groupBy(_ % 3) // Map(2 -> List(2, 5), 1 -> List(1, 4), 0 -> List(3, 6))
    List(1,2,3,4,5).grouped(2).toList // List(List(1, 2), List(3, 4), List(5))
    

    * * *

    &nbsp;

    List(1,2,3).forall(_>0)  // true
    List(1,2,3).exists(_<0)  // false
    List(1,2,3).foldLeft(10)(_+_) // 16
    (10 /: List(1,2,3))(_+_) // 16
    List(1,2,3).foldRight(10)(_+_) // 16
    (List(1,2,3) :\ 10)(_+_) // 16
    "abcde" sortWith (_ > _) // edcba
    

    * * *

    ## 问题1: 看懂创建List

    * * *

    ### val list = List(&#8220;aaa&#8221;,&#8221;bbb&#8221;,&#8221;ccc&#8221;)

    * * *

    等价于

    val list = List.apply("aaa","bbb","ccc")
    

    所以`List`是一个`object`

    * * *

    ### List可能是一个单独的`object`：

    object List {
        def apply[T](items: T*) = ???
    }
    

    * * *

    ### 或者是一个`case class`

    （编译器将会生成一个`object`）

    case class List[T](items: T*)
    

    * * *

    &nbsp;

    case class List[T](items: T*)
    

    等价于：

    class List[T](items: T*)
    object List {
        def apply[T](items: T*) = ???
    }
    

    * * *

    ## Scala用的是哪一种？

    object List {
        def apply[T](items: T*) = ???
    }
    

    因为它需要定义很多帮助方法，并且还有一个对应的`trait List`

    * * *

    ## 问题2: 关于List类型的推断

    * * *

    ## List[String]

    val list = List("aaa","bbb","ccc")
    

    * * *

    ## List[Int]

    val list2 = List(111,222,333)
    

    * * *

    ## List[Any]

    val list3 = List(111,"bbb")
    

    * * *

    ## 指定类型

    val list = List.empty[String]
    // List[String]
    

    * * *

    ## 它的类型呢？

    val list4 = List()
    

    * * *

    ## List[Nothing]

    val list1 = List.empty
    val list2 = List()
    

    * * *

    ## `Nil`，等价于`List()`

    其定义为：

    object Nil extends List[Nothing]
    

    * * *

    ### 它们可以赋给任意类型的List:

    val list:List[String]  = List.empty

    val list2:List[Int] = Nil
    

    * * *

    ## 为什么可以赋值?

1.  `Nothing`是任意类型的子类型
2.  `List[T]`是协变的 (`List[+T]`)

    * * *

    ## 问题3: 理解List的协变

    * * *

    ### 协变记法

    `List[+T]`，其中的`+`表示`List`相对于`T`是协变的

    * * *

    ### 协变的性质

    对于协变的`List[+T]`，当`C`是`P`的子类型时，则`List[C]`是`List[P]`的子类型。

    * * *

    ### 子类型定义

    由子类型定义（是什么？）得知：需要`List[P]`的地方，我们总是可以用`List[C]`的实例代替

    * * *

    ### 所以，我们总是能把:

1.  `List[String]`的实例赋给`List[Any]`
2.  `List[Nothing]`的实例赋给任意的`List[T]`

    * * *

    通常**不可变**的泛型，都可以声明为协变的（这里不讲）

    * * *

    ## 问题4: 向&#8221;大List&#8221;尾巴多次添加数据效率很低

    * * *

    需求：

    val bigList = (1 to 1000000).toList
    

    向尾巴依次添加从1到1000这些数

    * * *

    直接在尾巴添加：

    (1 to 1000).foldLeft(bigList) {
        case (list, item) => list :+ item
    }
    

    共用时**15349ms**

    * * *

    ### 先反转、在前面添加、再反转

    (1 to 1000).foldLeft(bigList.reverse) {
        case (list, item) => item :: list
    }.reverse
    

    共用时**47ms**

    * * *

    ## 换个思路（从前面加）

      bigList.foldRight(List.range(1,1001)) {
        case (item, list) => item :: list
      }
    

    共用时**89ms**

    * * *

    ## 留个问题，请认领

    为什么从前面加效率很高，从后面加效率很低？

    (请下节课用代码、图解等方式，为大家讲解这个问题)

    * * *

    ### 提示：List是不可变的链表结构

    class List[T](head:T, tail: List[T])
    

    * * *

    ## 问题5: 列表的匹配

    使用`List`:

    val list = List(1,2,3)

    list match {
      case List(a,b,c) => println(s"${a} ~ ${b} ~ ${c}")
    }
    //{1} ~ {2} ~ {3}
    

    * * *

    ### 使用`::`

    val list = List(1,2,3)

    list match {
        case head :: tail => println(s"${head} ~ ${tail}")
        case _ =>
    }
    // 1 ~ List(2, 3)
    

    * * *

    ### 赋值时匹配

    val List(a, b, c) = List(1,2,3)
    // a==1, b==2, c==3
    

    * * *

    ### 两种方式使用`::`

    val ::(a, tail) = List(1,2,3)
    // a==1, tail==List(2,3)

    val a :: tail = List(1,2,3)
    // a==1, tail==List(2,3)
    

    * * *

    ## 如何理解`::`?

    * * *

    ### 举个例子

    case class Hello(a:String, b:String)

    Hello("aaa", "bbb") match {
      case Hello(a,b) => println(a + ": " + b)
    }
    // aaa: bbb

    Hello("aaa", "bbb") match {
      case a Hello b => println(a + ": " + b)
    }
    // aaa: bbb
    

    (把这里的`Hello`替换为`::`即可)

    * * *

    ### 中缀类型

    trait Hello[A,B]

    def world: Hello[String, Int] = ???
    // world: Hello[String,Int]

    def anotherWorld: String Hello Int  = ???
    // anotherWorld: Hello[String,Int]
    

    （跟pattern matching无关）

    * * *

    ### pattern matching时的中缀表达

    object Hello {
      def unapply(s:String):Option[(String, String)] = ???
    }

    "hello" match {
      case a Hello b => println(a + ": " + b)
    }
    

    * * *

    ### 如果参数>2

    object Hello {
      def unapply(s:String):Option[(String, String, String)] = ???
    }

    "hello" match {
      case a Hello (b,c) => println(a + ": " + b + ": " + c)
    }
    

    通常用作中缀的objec名称，都是操作符，比如`::`, `~>`

    * * *

    ## 再看一遍`::`

    * * *

    ### 创建List

    val list = 1 :: 2 :: 3 :: Nil
    

    ### 模式匹配：

    list match {
      case a :: b :: Nil =>
    }

    list match {
      case ::(a, b) =>
    }
    

    * * *

    ## 这三个`::`是不是同一个`::`?

    * * *

    ### 方法List.::

    1 :: 2 :: 3 :: Nil
    

    定义：

    abstract class List[+A] {
      def ::[B >: A] (x: B): List[B] =
        new scala.collection.immutable.::(x, this)
    }
    

    * * *

    ## 右结合

    1 :: (2 :: (3 :: Nil))

    Nil.::(3).::(2).::(1)
    

    * * *

    ### case class ::

    case a :: b :: Nil =>
    case ::(a, b) =>
    

    定义：

    final case class ::[B](private var hd: B, private[scala] var tl: List[B]) extends List[B] {    … }
    

    * * *

    ## 问题6： 哪个能匹配上？

    List(1,2) match {
      case a :: b :: Nil =>
      case a :: b =>
      case ::(a, b) => 
    }
    

    * * *

    ### 这个呢

    List(1,2,3) match {
      case a :: b :: Nil =>
      case a :: b =>
      case ::(a, b) => 
    } 
    

    * * *

    ## 等价

    a :: b == :: (a, b)
    a :: b :: Nil == :: (a, ::(b, Nil))
    

    * * *

    ## 问题7：无法匹配, why?

    val List(a, tail) = List(1,2,3)
    

    * * *

    ## 如何找线索

    case class User(name:String, friends: Seq[String])

    user match {
      case User(n, fs) => 
    }
    // 这里fs一定是Seq[String]类型
    

    * * *

    ## 通过unapply的返回值

    object Hello {
      def unapply(s:String): Option[(String, List[String])] = ???
    }

    "some-words" match {
      case Hello(a,b) =>
    }
    // b 一定是 List[String]
    

    * * *

    ## List的线索在哪里？

    val List(a, tail) = List(1,2,3)

    // 将会调用
    List.unapplySeq(List(1,2,3))

    // 并根据其返回值类型与List(a,tail)匹配

    // unapplySeq定义
    def unapplySeq[A](x: Seq[A]): Option[Seq[A]] = Some(x)

    // 其返回值中包含的还是Seq[A]
    

    线索似乎断了~

    * * *

    ## 线索就是unapplySeq

    scala规范中规定了，对于

    def unapplySeq(…):Option[Seq[A]]

匹配有两种规则：

1.  List(a,b) 表示匹配一个长度为2的List，a为第一个元素，b为第二个
2.  List(a,b@_*) 表示匹配一个长度>1的list，a为第一个元素，b为后面全部

* * *

## 更多问题，欢迎大家领取：

1.  Set
2.  Map
3.  Tuple
4.  Option
5.  Array
6.  前面的为什么List右边插数据比左边慢？
