---
layout: post
id: 482
alias: use-jvisualvm-to-find-deadlocks
tags: Java
date: 2011-10-23 13:25:18
title: 利用jvisualvm检测程序死锁
---

刚才在群里介绍了JVisualVM的功能，sungod问：&#8221;它能检测到线程死锁吗&#8221;

所以我专门写了一个简单的死锁程序，看看jvisualvm的反应。

这里我考虑的是一种简单的死锁情况：线程1拿到了a的锁，准备拿b的锁；同时线程2拿到了b的锁，准备拿a的锁。两个线程谁也不让，把自己的既得利益抓得紧紧的，然后会抢对方的。于是僵持不下，程序就死锁了。

 <span id="more-482"></span>
<p>代码如下：

```
package test;

public class DeadLock {

    public static void main(String[] args) {
        final Object a = new Object();
        final Object b = new Object();

        new Thread() {
            @Override
            public void run() {
                try {
                    synchronized (a) {
                        System.out.println("Thread 1 got the lock of a");
                        Thread.sleep(1000);
                        System.out.println("Thread 1 was trying to get the lock of b");
                        synchronized (b) {
                            System.out.println("Thread 1 win");
                        }
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }.start();
        new Thread() {
            @Override
            public void run() {
                try {
                    synchronized (b) {
                        System.out.println("Thread 2 got the lock of b");
                        Thread.sleep(1000);
                        System.out.println("Thread 2 was trying to get the lock of a");
                        synchronized (a) {
                            System.out.println("Thread 2 win");
                        }
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }.start();
    }
}
```

程序打印出以下内容：

> Thread 1 got the lock of a 
> Thread 2 got the lock of b 
> Thread 1 was trying to get the lock of b 
> Thread 2 was trying to get the lock of a

然后就卡住了。

现在打开jvisualvm，如下图：

![](http://freewind.me/wp-content/uploads/2011/10/zrclip_019p18edecfd.png)

可以看到，Thread1与2两个都处于&#8221;监视&#8221;状态。

点一下&#8221;线程Dump&#8221;按钮，找到Thread1与2的信息，如下：

![](http://freewind.me/wp-content/uploads/2011/10/zrclip_020n66fb9b3b.png)

从中可以得知，两者都处于&#8221;Blocked&#8221;状态，并且知道阻塞在哪一行代码上。

我在jvisualvm中没有找到明确说明两者都处于&#8221;死锁&#8221;状态的文字，但从线程图中，我们可以很轻易地找到这种可疑特征：两个（或多个）线程长期同时处于&#8221;监视（被阻塞）&#8221;状态，说明它们很可能出现了死锁，应当认真分析其代码。

我想这对于我们检查死锁也是非常有帮助的。
