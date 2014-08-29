---
layout: post
tags: Scala
date: 2012-02-11 21:58:37
title: scala中Iterator的比较
---

这是老猪布置的作业：在scala中，如何比较两个iterator是否相等。

Scala中的Iterator，可用来依次取下一个数据。比如：

> scala> val it = List("aaa","bbb","ccc").iterator      
> it: Iterator[java.lang.String] = non-empty iterator
> 
> scala> it.next      
> res1: java.lang.String = aaa
> 
> scala> it.next      
> res2: java.lang.String = bbb
> 
> scala> it.next      
> res3: java.lang.String = ccc

它有一个特点，叫TraversableOnce，就像单行道，只能向前走，不能回头。甚至像size这样的方法，一旦调用，iterator内部指针就跑到最尾，不能再用只能丢了。

> scala> val it = List("aaa","bbb","ccc").iterator      
> it: Iterator[java.lang.String] = non-empty iterator
> 
> scala> it.size      
> res4: Int = 3
> 
> scala> it.next      
> java.util.NoSuchElementException: next on empty iterator       
>         at scala.collection.Iterator$$anon$3.next(Iterator.scala:28)
> 
>  

所以在比较两个iterator时，必须考虑这一点。

是否可以直接通过==来比较？比如：

> <font style="background-color: #ffffff">it1 == it2</font>

答案是不行。不知道Iterator的equals方法内部是如何实现的，但是对于两个相等的Iterators，它返回的是false

目前只想到三种方法：

1. Iterator本身提供的sameElements方法，用来比较两个iterator是否相等

2. 把它们转为集合，如it.toList，再比较

3. 手动遍历两个Iterator，依次比较

代码如下：

> object IteratorCmp {
> 
>   def compare1(it1: Iterator[String], it2: Iterator[String]): Boolean = {      
>     it1.toList == it2.toList       
>   }
> 
>   def compare2(it1: Iterator[String], it2: Iterator[String]): Boolean = {      
>     it1 sameElements it2       
>   }
> 
>   def compare3(it1: Iterator[String], it2: Iterator[String]): Boolean = {      
>     while (it1.hasNext) {       
>       if (!it2.hasNext) return false       
>       if (it1.next != it2.next) {       
>         return false       
>       }       
>     }       
>     return !it2.hasNext       
>   }
> 
>   def main(args: Array[String]) {      
>     val list1 = List("aaa", "bbb", "ccc", "ddd", "eee", "fff", "ggg", "hhh")       
>     val list2 = List("aaa", "bbb", "ccc", "ddd", "eee", "fff", "ggg", "hhh")       
>     val list3 = List("aaa", "bbb", "ccc", "ddd", "eee", "fff", "ggg")       
>     val list4 = List("aaa", "bbb", "ccc", "ddd", "eee", "fff", "ggg", "hhh", "iii")
> 
>     test(compare1(list1.iterator, list2.iterator))      
>     test(compare2(list1.iterator, list2.iterator))       
>     test(compare3(list1.iterator, list2.iterator))
> 
>     test(compare1(list1.iterator, list3.iterator))      
>     test(compare2(list1.iterator, list3.iterator))       
>     test(compare3(list1.iterator, list3.iterator))
> 
>     test(compare1(list1.iterator, list4.iterator))      
>     test(compare2(list1.iterator, list4.iterator))       
>     test(compare3(list1.iterator, list4.iterator))       
>   }
> 
>   def test(runnable: => Boolean) {      
>     var result = false       
>     val start = System.nanoTime       
>     for (i <- 1 to 100000) {       
>       result = runnable       
>     }       
>     val end = System.nanoTime       
>     println(result + " ==> Cost: " + (end - start) / 1000000.0 + " ms")       
>   }
> 
> }
> 
>  

运行结果如下：

> true ==> Cost: 150.555308 ms      
> true ==> Cost: 96.055434 ms       
> true ==> Cost: 90.490901 ms       
> false ==> Cost: 124.912105 ms       
> false ==> Cost: 82.382677 ms       
> false ==> Cost: 86.721566 ms       
> false ==> Cost: 136.254817 ms       
> false ==> Cost: 92.202501 ms       
> false ==> Cost: 88.270367 ms       
> 
>  

可以看到，toList的方法用的时间最长，其它两种差不多。

不知道有没有其它更好的方法来比较，欢迎推荐：）

注：老猪最后给出了一种更加函数式风格的代码，如下：

> def eq_?[T](i1: Iterator[T], i2: Iterator[T]) = !i1.exists{e =>      
>   !i2.hasNext || e != i2.next || (!i1.hasNext && i2.hasNext)       
> }

其中没有用while等，代码更加紧凑流畅。

另外发现sameElements的实现，与我的代码比较相似，只是看起来更加清楚简洁一些：

> def sameElements(that: Iterator[_]): Boolean = {    
>   while (hasNext && that.hasNext)       
>     if (next != that.next)       
>       return false       
>   
>   !hasNext && !that.hasNext       
> }       
> 
>  

不过在性能上，我的第三种方法，在大多数情况下是最快的，因为里面的第一个return，能让代码更快的返回。

最后感谢老猪的指导。

更新：在stackoverflow.com上提了问，见：[http://stackoverflow.com/questions/9240936/how-to-compare-two-iterators-in-scala](http://stackoverflow.com/questions/9240936/how-to-compare-two-iterators-in-scala)

有网友给出了其它的写法。

1. 使用zip

> `def compare(it1:Iterator[String], it2:Iterator[String]) = {       
>   it1.zip(it2).forall(x => x._1 == x._2) &&         
>   (it1.length == it2.length)         
> }`

`2. 使用递归`

> def compare(it1:Iterator[String], it2:Iterator[String]) : Boolean = {
>       (it1 hasNext, it2 hasNext) match{
>         case (true, true) => (it1.next == it2.next) && compare(it1, it2)
>         case (false, false) => true
>         case _ => false
>       }
>     }
<pre>```