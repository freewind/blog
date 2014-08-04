---
layout: post
title: ArrayList.add在扩容时花费的时间
tags:
  - Java
date: 2011-10-31 10:51:35
---

ArrayList内部由一个数组来持有数据，其长度固定。当add的数据超过了当前数组长度，ArrayList会生成一个长度为原有1.5倍的新数组，把旧数据拷贝过去。该操作发生的可能性比较小，但它花费的时间较多。

<span id="more-538"></span>
<p>具体有多少呢？写代码测试：

> public class ListAddPerf { 
> 
>     public static void main(String[] args) {      
>         List<Long> list = new ArrayList<Long>();       
>         for (int i = 0;; i++) {       
>             long start = System.nanoTime();       
>             list.add(start);       
>             long end = System.nanoTime();       
>             long cost = end - start;       
>             if (cost > 1000000) {       
>                 System.out.println("Index: " + i + ", Cost: " + cost + "ns" + " = " + cost / 1000000 + "ms");       
>             }       
>         }       
>     }       
> }

该代码从0开始，向ArrayList中加入元素并计时，如果超过1ms，则显示出来。测试结果如下：

> Index: 132385, Cost: 14285668ns = 14ms      
> Index: 198578, Cost: 1236917ns = 1ms       
> Index: 285095, Cost: 18650207ns = 18ms       
> Index: 297868, Cost: 2062708ns = 2ms       
> Index: 446803, Cost: 23438783ns = 23ms       
> Index: 561806, Cost: 94347099ns = 94ms       
> Index: 670205, Cost: 4009072ns = 4ms       
> Index: 834672, Cost: 37478150ns = 37ms       
> Index: 1005308, Cost: 141769921ns = 141ms       
> Index: 1500690, Cost: 54481688ns = 54ms       
> Index: 1507963, Cost: 9182793ns = 9ms       
> Index: 1807569, Cost: 264185533ns = 264ms       
> Index: 2261945, Cost: 14390365ns = 14ms       
> Index: 2573063, Cost: 111484680ns = 111ms       
> Index: 3392918, Cost: 514175775ns = 514ms       
> Index: 4170442, Cost: 1168867ns = 1ms       
> Index: 4958990, Cost: 175187890ns = 175ms       
> Index: 5089378, Cost: 31229200ns = 31ms       
> Index: 5888911, Cost: 1039109580ns = 1039ms       
> Index: 7634068, Cost: 200057398ns = 200ms       
> Index: 9248161, Cost: 1136859792ns = 1136ms
> 
>  

可以看到，在一千万次调用中，仅仅发生了20次左右，但每次花的时间都不少，特别是到后来甚至超过1s。（运行此程序时，我没有填写Xmx参数，所以后来可能发生较多的full gc）

如果我们对于每一次写入的性能都很看重，此时应该考虑一下是否能接受这些偶发的较慢操作。

如果在创建ArrayList时指定了初始容量（此时设为1000000），发现扩容操作更少了，结果如下：

> Index: 280890, Cost: 26529072ns = 26ms      
> Index: 567492, Cost: 36645133ns = 36ms       
> Index: 854138, Cost: 157540178ns = 157ms       
> Index: 3050324, Cost: 237405247ns = 237ms       
> Index: 5278473, Cost: 749483129ns = 749ms       
> Index: 5280609, Cost: 2085053ns = 2ms       
> Index: 8046708, Cost: 2094405ns = 2ms       
> Index: 8232630, Cost: 1026309ns = 1ms       
> Index: 8341925, Cost: 2187398ns = 2ms       
> Index: 9486066, Cost: 1958284549ns = 1958ms
> 
>  

只发生了几次略大的延时，性能比前面略好，但它会始终占用较大的内存，需要权衡。