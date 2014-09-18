---
layout: post
id: 534
alias: jvisualvm-profiler-impacts-synchronized
tags: Java
date: 2011-10-29 12:50:15
title: jvisualvm的profiler分析对synchronized的影响
---

昨天运行MDF程序时，使用jsivualvm进行分析。开始发现各线程未出现阻塞状态，但当点击了Profiler项的CPU性能分析之后，线程出现了明显且持续的阻塞。

因为Profiler的CPU分析需要计算每个方法的调用次数以及运行的时间，所以在内部会进行大量的工作。它会不会导致无synchronized的代码出现阻塞？或者加剧阻塞机率？这里写代码测试一下。

<span id="more-534"></span>

**一、完全不会阻塞的多线程程序**

> import java.util.concurrent.atomic.AtomicLong; 
> 
> public class NoSynTest { 
> 
>     public static void main(String[] args) throws Exception {      
>         // 让jvivualvm有足够的时间做准备工作       
>         Thread.sleep(5000); 
> 
>         final AtomicLong count = new AtomicLong(0);      
>         for (int i = 0; i < 2; i++) {       
>             new Thread() {       
>                 public void run() {       
>                     while(true) {       
>                         count.incrementAndGet();       
>                     }       
>                 };       
>             }.start();       
>         }       
>     }       
> }

jvisualvm在开启了Profiler之后的线程图如下：

[![image](/user_images/534-1.png "image")](/user_images/534-1.png) 

此处的Thread-7和Thread-6就是工作线程，可以看出，全为绿色，表示正常运行。

二、**有synchronized的多线程程序，不开Profiler**

> public class SynTest { 
> 
>     public static void main(String[] args) throws Exception {      
>         // 让jvivualvm有足够的时间做准备工作       
>         Thread.sleep(5000); 
> 
>         final AtomicLong count = new AtomicLong(0);      
>         for (int i = 0; i < 2; i++) {       
>             new Thread() {       
>                 public void run() {       
>                     while(true) {       
>                         synchronized (count) {       
>                             count.incrementAndGet();       
>                         }       
>                     }       
>                 };       
>             }.start();       
>         }       
>     }       
> }

没开Profiler时的线程状态：

[![image](/user_images/534-3.png "image")](/user_images/534-3.png) 

三、**有synchronized的多线程程序，开Profiler**

[![image](/user_images/534-5.png "image")](/user_images/534-5.png)

与二对比，可以看到仍然有阻塞现象，但并无“加剧”表现。

注：此处测试比较麻烦，因为当程序运行时，jvisualvm反应很慢，一次简单测试要等十五分钟以上，甚至一直卡住无反应。

**四、拿MDF程序测试，不开Profiler**

[![image](/user_images/534-7.png "image")](/user_images/534-7.png) 

可以看到完全没有阻塞。

**五、开Profiler**

[![image](/user_images/534-9.png "image")](/user_images/534-9.png) 

可以看到，的确有阻塞现象发生。说明jvivualvm的确可加剧阻塞的产生。而在第二步第三步的测试中，都出现了阻塞，可能是因为代码写得比较容易产生阻塞，所以在平时状态下也会出现。

但是如何修改代码，才能模拟出MDF的这种状态不太容易，所以不继续深究。因为已经可以得出结论：**jvisualvm不会无中生有产生阻塞，但会加剧阻塞的产生**。
