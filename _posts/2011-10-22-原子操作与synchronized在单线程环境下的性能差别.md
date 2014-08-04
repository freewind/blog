title: 原子操作与synchronized在单线程环境下的性能差别
tags:
  - Java
date: 2011-10-22 20:55:32
---

synchronized因为多了一个&#8221;加锁&#8221;的过程，会占用额外的时间，导致性能较差。特别是在多线程环境中，如果存在竞争，性能会更差一些。但是到底有多差呢？

我们先看看在单线程环境中，它的表现如何。这里使用了三种不同的方式：

*   普通变量加1
*   原子变量加1
*   先synchronized，再加1

代码如下：

 <span id="more-457"></span> 

```
public class IncTest1 {

    public static void main(String[] args) {
        // simple variable
        int simple = 0;
        long start = System.nanoTime();
        for (int i = 0; i < 100000000; i++) {
            simple = simple + 1;
        }
        long end = System.nanoTime();
        System.out.println("simple inc cost: " + (end - start) / 1000000.0 + "ms");

        // atom
        AtomicInteger atom = new AtomicInteger();
        start = System.nanoTime();
        for (int i = 0; i < 100000000; i++) {
            atom.incrementAndGet();
        }
        end = System.nanoTime();
        System.out.println("atom inc cost: " + (end - start) / 1000000.0 + "ms");

        // synchronized
        int syn = 0;
        start = System.nanoTime();
        for (int i = 0; i < 100000000; i++) {
            synchronized (IncTest1.class) {
                syn = syn + 1;
            }
        }
        end = System.nanoTime();
        System.out.println("snchronized inc cost: " + (end - start) / 1000000.0 + "ms");

    }
}
```

其运行结果如下：

> simple inc cost: 146.260704ms 
> atom inc cost: 1473.678995ms 
> synchronized inc cost: 2568.035ms

从中可以看出，原子操作是普通变量的10倍，而synchronized是原子操作的近2倍。

在单线程不存在竞争的情况下，synchronized的性能都是最差的，可见我们的确应尽量减少synchronized的使用。