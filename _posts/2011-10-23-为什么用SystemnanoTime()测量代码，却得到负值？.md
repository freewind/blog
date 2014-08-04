title: 为什么用System.nanoTime()测量代码，却得到负值？
tags:
  - Java
date: 2011-10-23 20:50:33
---

从之前的几篇日志中，我知道应该使用System.nanoTime()来测量代码的运行时间，会比较准确。但是我在使用过程中发现，有时候居然得到了负值，怎么回事？

代码如下：

```
public static void main(String[] args) throws Exception {
    while (true) {
        long start = System.nanoTime();
        for (int i = 0; i < 10000; i++)
            ;
        long end = System.nanoTime();
        long cost = end - start;
        if (cost < 0) {
            System.out.println("start: " + start + ", end: " + end + ", cost: " + cost);
        }
    }
}
```

得到了如下的结果：

> start: 34571588742886, end: 34571585695366, cost: -3047520 
> start: 34571590239323, end: 34571586847711, cost: -3391612 
> start: 34571651240343, end: 34571648928369, cost: -2311974 
> start: 34571684937094, end: 34571681543134, cost: -3393960 
> start: 34571791867954, end: 34571788878081, cost: -2989873 
> start: 34571838733068, end: 34571835464021, cost: -3269047 
> start: 34571869993665, end: 34571866950949, cost: -3042716 
> start: 34571963747021, end: 34571960656216, cost: -3090805 
> start: 34571965020545, end: 34571961637608, cost: -3382937 
> start: 34572010616580, end: 34572007613257, cost: -3003323

为什么，居然有负值？！这让人怎么去测量代码运行时间啊。

*   操作系统：windows xp sp3
*   Java: jdk1.6u27

最终在万能的stackoverflow上得到了答案：

*   [http://stackoverflow.com/questions/7866206/why-i-get-a-negative-elapsed-time-using-system-nanotime](http://stackoverflow.com/questions/7866206/why-i-get-a-negative-elapsed-time-using-system-nanotime)*   [http://stackoverflow.com/questions/510462/is-system-nanotime-completely-useless](http://stackoverflow.com/questions/510462/is-system-nanotime-completely-useless)

其原因是在多核机器上，每个核的启动时间不完全一致。在我的例子中，大约相差3ms。其它的操作系统会自己修正这个时间差，而windows xp比较笨。所以会有这个问题。

另外据网友们在各平台上的测试，目前只发现windows xp有这个问题。