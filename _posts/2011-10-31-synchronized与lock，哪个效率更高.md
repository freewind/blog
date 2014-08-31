---
layout: post
id: 542
alias: which-has-better-performance-synchronized-and-lock
tags: 未分类
date: 2011-10-31 21:26:02
title: synchronized与lock，哪个效率更高
---

Java在一开始就提供了synchronized关键字，用于多线程之间的同步。它使用简便，不会出现拿锁之后不归还的情况，可以避免一些编程错误。

而jdk5时提供的concurrent包里，有一个Lock接口以及它的实现类：ReentrantLock。这个类提供了更灵活的控制以及更强大的功能。

如果单从性能方面考虑，两个哪个更高效呢？

<span id="more-542"></span>

首先是单线程的加锁情况，见以下代码：

> import java.util.concurrent.locks.Lock;      
> import java.util.concurrent.locks.ReentrantLock; 
> 
> public class SynLockTest { 
> 
>     public static void main(String[] args) {      
>         long value = 0;       
>         int MAX = 10000000;       
>         Lock lock = new ReentrantLock();       
>         long start = System.nanoTime();       
>         for (int i = 0; i < MAX; i++) {       
>             synchronized (new Object()) {       
>                 value = value + 1;       
>             }       
>         }       
>         long end = System.nanoTime();       
>         System.out.println("synchronized cost: " + (end - start)/1000000 + "ms"); 
> 
>         start = System.nanoTime();      
>         for (int i = 0; i < MAX; i++) {       
>             lock.lock();       
>             try {       
>                 value = value + 1;       
>             } finally {       
>                 lock.unlock();       
>             }       
>         }       
>         end = System.nanoTime();       
>         System.out.println("lock cost: " + (end - start) + "ns");       
>     }       
> }

结果如下：

> synchronized cost: 405ms      
> lock cost: 479ms

可见Lock的运行时间比synchronized略大。可以推测java编译器为synchronized做了特别优化。

再考虑多线程情况：

> public class SynLockTest { 
> 
>     static class SynRunner implements Runnable {      
>         private long v = 0; 
> 
>         @Override      
>         public synchronized void run() {       
>             v = v + 1;       
>         }       
>     } 
> 
>     static class LockRunner implements Runnable {      
>         private ReentrantLock lock = new ReentrantLock();       
>         private long v = 0; 
> 
>         @Override      
>         public void run() {       
>             lock.lock();       
>             try {       
>                 v = v + 1;       
>             } finally {       
>                 lock.unlock();       
>             }       
>         } 
> 
>     } 
> 
>     static class Tester {      
>         private AtomicLong runCount = new AtomicLong(0);       
>         private AtomicLong start = new AtomicLong();       
>         private AtomicLong end = new AtomicLong(); 
> 
>         public Tester(final Runnable runner, int threadCount) {      
>             final ExecutorService pool = Executors.newFixedThreadPool(threadCount);       
>             Runnable task = new Runnable() {       
>                 @Override       
>                 public void run() {       
>                     while (true) {       
>                         runner.run();      
>                         long count = runCount.incrementAndGet();       
>                         if (count == 1) {       
>                             start.set(System.nanoTime());       
>                         } else if (count >= 10000000) {       
>                             if (count == 10000000) {       
>                                 end.set(System.nanoTime());       
>                                 System.out.println(runner.getClass().getSimpleName() + ", cost: "       
>                                         + (end.longValue() - start.longValue())/1000000 + "ms");                            }       
>                                 pool.shutdown();       
>                             return;       
>                         }       
>                     }       
>                 }       
>             };       
>             for (int i = 0; i < threadCount; i++) {       
>                 pool.submit(task);       
>             }       
>         }       
>     } 
> 
>     public static void main(String[] args) {      
>         new Tester(new SynRunner(), 1);       
>         new Tester(new LockRunner(), 1);       
>     } 
> 
> }
> 
>  

现在测试不同线程下的表现（时间单位ms）：

<table cellspacing="0" cellpadding="2" width="600" border="1">
<tbody>
<tr>
<td valign="top" width="75"> </td>
<td valign="top" width="75">1</td>
<td valign="top" width="75">10</td>
<td valign="top" width="75">50</td>
<td valign="top" width="75">100</td>
<td valign="top" width="75">500</td>
<td valign="top" width="75">1000</td>
<td valign="top" width="75">5000</td>
</tr>
<tr>
<td valign="top" width="75">synchronized</td>
<td valign="top" width="75">542</td>
<td valign="top" width="75">4894</td>
<td valign="top" width="75">4667</td>
<td valign="top" width="75">4700</td>
<td valign="top" width="75">5151</td>
<td valign="top" width="75">5156</td>
<td valign="top" width="75">5178</td>
</tr>
<tr>
<td valign="top" width="75">lock</td>
<td valign="top" width="75">838</td>
<td valign="top" width="75">1211</td>
<td valign="top" width="75">821</td>
<td valign="top" width="75">847</td>
<td valign="top" width="75">851</td>
<td valign="top" width="75">1211</td>
<td valign="top" width="75">1241</td>
</tr>
</tbody>
</table>

可以看到，在多线程环境并存在大量竞争的情况下，synchronized的用时迅速上升，而lock却依然保存不变或增加很少。
