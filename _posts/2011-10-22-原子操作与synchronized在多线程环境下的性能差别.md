title: 原子操作与synchronized在多线程环境下的性能差别
tags:
  - Java
date: 2011-10-22 22:05:03
---

从另一篇两者在单线程环境下的测试可以看出，就算没有竞争，synchronized也比原子操作慢。地址如下：[http://freewind.me/blog/20111022/457.html](http://freewind.me/blog/20111022/457.html)

这里测试多线程环境下的影响。重构了代码，如下：

 <span id="more-458"></span> 

```
public class IncTestN {

    public static int MAX_COUNT = 50000000;
    public static int THREADS = 1;

    interface Incer {
        long inc();
    }

    static class AtomIncer implements Incer {
        private AtomicLong atom = new AtomicLong();

        @Override
        public long inc() {
            return atom.incrementAndGet();
        }
    }

    static class SynIncer implements Incer {
        private long syn = 0;

        @Override
        public synchronized long inc() {
            syn += 1;
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

通过修改THREADS的值，在我的电脑上可以得到以下数据：

<table width="650">
<tbody>
<tr>
<td>线程数</td>
<td>1</td>
<td>2</td>
<td>5</td>
<td>10</td>
<td>20</td>
<td>50</td>
</tr>
<tr>
<td>atom</td>
<td>1413ms</td>
<td>5066ms</td>
<td>5226ms</td>
<td>5283ms</td>
<td>5328ms</td>
<td>5248ms</td>
</tr>
<tr>
<td>synchronized</td>
<td>1913ms</td>
<td>24560ms</td>
<td>25895ms</td>
<td>25507ms</td>
<td>25172ms</td>
<td>25459ms</td>
</tr>
</tbody>
</table>

可见，当变为多线程后，两者的用时都变多了，而synchronized受到的影响要大得多。

但从表中发现，2个线程与50个线程用时差不多。这又应该如何解释呢？

我想可能是因为我们的逻辑代码比较简单，在一个线程运行的时间内，就足以运行大量的循环，所以线程的切换与竞争，没并有想像中那么频繁。但这也已经让性能受到了很大的影响。

所以在代码中，还是要尽量避免或减少synchronized的使用。这种性能问题很难测试出来。