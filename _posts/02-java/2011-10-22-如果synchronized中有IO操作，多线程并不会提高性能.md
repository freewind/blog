---
layout: post
id: 460
alias: multithread-wont-get-better-performance-if-there-are-io-operations-in-synchronized-code
tags: Java
date: 2011-10-22 23:17:04
title: 如果synchronized中有IO操作，多线程并不会提高性能
---

在之前的文章&#8221;[原子操作与synchronized在多线程环境下的性能差别](http://freewind.me/blog/20111022/458.html)&#8220;中，发现在2个线程与50个线程情况下，程序运行时间并没有什么差别。我考虑的原因是，因为我synchronized中的代码都很简单，所以线程间竞争的机会并不大。

林教主让我在代码中增加一些sleep(1)，来模拟IO操作。于是有了以下的代码：

 <span id="more-460"></span> 

```
public class IncTestN {

        public static int MAX_COUNT = 5000;
        public static int THREADS = 50;

        interface Incer {
                long inc() throws Exception;
        }

        static class AtomIncer implements Incer {
                private AtomicLong atom = new AtomicLong();

                @Override
                public long inc() throws Exception {
                        long v = atom.incrementAndGet();
                        Thread.sleep(1);
                        return v;
                }
        }

        static class SynIncer implements Incer {
                private long syn = 0;

                @Override
                public synchronized long inc() throws Exception {
                        syn += 1;
                        Thread.sleep(1);
                        return syn;
                }
        }

        static class Tester {
                ExecutorService pool = Executors.newFixedThreadPool(THREADS);
                Incer incer;
                final AtomicLong start = new AtomicLong(0);
                final AtomicLong end = new AtomicLong(0);

                public Tester(Incer incer) {
                        this.incer = incer;
                }

                public long test() throws InterruptedException {
                        Callable<Void> task = new Callable<Void>() {
                                @Override
                                public Void call() throws Exception {
                                        while (true) {
                                                long v = incer.inc();
                                                if (v == 1) {
                                                        start.set(System.nanoTime());
                                                } else if (v == MAX_COUNT) {
                                                        end.set(System.nanoTime());
                                                        break;
                                                } else if (v > MAX_COUNT) {
                                                        break;
                                                }
                                        }
                                        return null;
                                }
                        };
                        List<Callable<Void>> list = new ArrayList<Callable<Void>>();
                        for (int i = 0; i < THREADS; i++) {
                                list.add(task);
                        }
                        pool.invokeAll(list);
                        long cost = (end.longValue() - start.longValue()) / 1000000;
                        pool.shutdownNow();
                        return cost;
                }
        }

        public static void main(String[] args) throws Exception {
                long atomCost = new Tester(new AtomIncer()).test();
                System.out.println("Atom cost: " + atomCost + "ms");

                long synCost = new Tester(new SynIncer()).test();
                System.out.println("Synchronized cost: " + synCost + "ms");
        }
}
```

注意这段代码，与那篇文章中的代码，并没有大的不同，只不过每个inc()方法中增加了一个Thread.sleep(1)，模拟一个IO操作。同时将inc的max值改为5000，不然会等死。

现在再运行程序，得到结果如下：

<table width="350">
<tbody>
<tr>
<td>线程数</td>
<td>1</td>
<td>10</td>
<td>50</td>
</tr>
<tr>
<td>atom</td>
<td>9770ms</td>
<td>975ms</td>
<td>198ms</td>
</tr>
<tr>
<td>synchronized</td>
<td>9768ms</td>
<td>9762ms</td>
<td>9766ms</td>
</tr>
</tbody>
</table>

可以看到，当线程数增多时，atom的运行时间大大减少，而synchronized没有变化。这是因为atom中多线程没有竞争，每个线程都会处于运行状态，减少了运行时间。而synchronized的，多个线程都要经过synchronized这条独木桥，实际上成了单线程。

所以要注意，如非万分必要，一定不要在synchronized中进行耗时的操作（如IO操作，数据库等），否则会大大影响程序的性能。
