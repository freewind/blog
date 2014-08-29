---
layout: post
title: windowsxp上的System.currentTimeMillis和System.nanoTime
tags: Java
date: 2011-10-22 20:00:42
---

今天在看以前代码时，突然想起一个问题：在windows xp上，使用System.currentTimeMillis来得到当前时间毫秒值，但是它的精度只有15ms到16ms，而不是1ms。

这个问题相当严重，因为我们平时经常会在代码中这样来计算代码的运行时间：

```
long start = System.currentTimeMillis();
// ... some code
long end = System.currentTimeMillis();
System.out.println("Your code cost: " + (end-start) + "ms");
```

可惜的是，如果你要测量的代码运行得比较快的话，就会发现，其值要么是0ms，要么是15ms。没有0-15ms之间的值。

 <span id="more-455"></span>
<p>我想到以前做过的一个项目，在日志中检查每一条信息接收时的毫秒值和发送时的毫秒值，以记录延时。但是统计时发现，要么就是0ms，要么就是15ms以上，中间这一块数据都去哪儿了？

所以如果要求很精确的话，这种方式是不可靠的（在windows上）。

在群友们的帮助下，在不同的操作系统中运行以下代码：

```
public class MillisTime {
    public static void main(String[] args) {
        long start = 0;
        long end = 0;
        while (true) {
            if (start == 0) {
                start = System.currentTimeMillis();
            } else {
                long current = System.currentTimeMillis();
                if (current != start) {
                    end = current;
                    break;
                }
            }
        }
        System.out.println("The time interval of your OS: " + (end - start) + "ms");
    }
}
```

得到的结果如下：

*   windows XP: 15ms
*   windows 7: 1ms
*   linux: 1ms
*   mac: 1ms

看来只有windows xp最菜。

毫秒不行，那纳秒呢？我们知道还有一个System.nanoTime()方法呢。（注：1ms = 1,000,000ns )

我猜想，在windows xp中，既然时间精度只有15ms，那么使用nanoTime应该也得到同样的值，即15000000ns。于是运行以下代码：

```
public class NanoTime {
        public static void main(String[] args) {
                long start = 0;
                long end = 0;
                while (true) {
                        if (start == 0) {
                                start = System.nanoTime();
                        } else {
                                long current = System.nanoTime();
                                if (current != start) {
                                        end = current;
                                        break;
                                }
                        }
                }
                System.out.println("The time interval of your OS: " + (end - start) + "ns");
        }
}
```

结果却是：

> The time interval of your OS: 763ns

奇怪，只有763ns，还不到0.0001ms呢，这是怎么回事？为什么1ms的间隔都得不到，却能得到0.0001ms的间隔？这个值不是瞎写的吧！

于是我到万能的stackoverflow上问了几个问题，得到了几位大仙们的回答，终于明白了这个问题：

*   System.currentTimeMillis得到的是一个绝对时间值，你可以从该值推算当前的时间，也可以从当前的时间推算出这个值。因为有这样的要求，所以它的精度不高，特别在windows xp上，只有15ms的精度。
*   而System.nanoTime是专门用来算间隔的。它的值跟当前时间没有任何关系，它甚至可以是负值。也正因为没有这个限制，使用它反而可以得到精度很高的间隔值。比如在windows xp sp3上，这个时间是可信的。也就是说，例子中的&#8221;763ns&#8221;说明那段代码的确运行了大约700ns。

明白这一点之后，我得出了以下结论：

如果我们要测量的代码运行时间很长，或者不是特别关心其准确度，可以使用System.currentTimeMillis图个方便。但如果要求很精确或者间隔很小，最好使用nanoTime。

前面提到的几个问题地址如下：

*   [http://stackoverflow.com/questions/7859019/system-currenttimemillis-is-not-accurate-on-windows-xp](http://stackoverflow.com/questions/7859019/system-currenttimemillis-is-not-accurate-on-windows-xp)
*   [http://stackoverflow.com/questions/7859043/system-nanotime-on-windows-xp](http://stackoverflow.com/questions/7859043/system-nanotime-on-windows-xp)
*   [http://stackoverflow.com/questions/510462/is-system-nanotime-completely-useless](http://stackoverflow.com/questions/510462/is-system-nanotime-completely-useless)

最后再想一下，System.currentTimeMillis得到不准确值，是Java的问题还是windows xp的问题？在xp上使用其它的语言（如C等），会是什么情况？

*   [http://stackoverflow.com/questions/7859417/how-accurate-of-system-currenttimemillis-on-winxp-with-other-language-than-java/7859457#7859457](http://stackoverflow.com/questions/7859417/how-accurate-of-system-currenttimemillis-on-winxp-with-other-language-than-java/7859457#7859457)

答案是，这是操作系统跟硬件的问题，与Java无关。
