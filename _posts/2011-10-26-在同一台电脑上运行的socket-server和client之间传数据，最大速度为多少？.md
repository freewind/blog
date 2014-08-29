---
layout: post
title: 在同一台电脑上运行的socket server和client之间传数据，最大速度为多少？
tags: Java
date: 2011-10-26 23:20:04
---

我对于Java的socket性能，其实一直都没有一个数值上的概念。比如，写一个最简单的socket server和client，它们什么也不做，就是server不断向client发数据，client收到后就丢弃，只计数算速度。

那么速度应该是多少呢？

注意，如果server和client在同一台电脑上，其速度不受网卡限制，因为数据不经过网卡，直接在内存中复制。没有了IO等待，测试的就是Java socket的性能。

在我的电脑上，最大值为170MBytes/s。我的电脑配置：

> AMD Athlon X2 4000+ 
> 4G DDR2 667 Ram 
> 500G Seagate 7200转

朋友的电脑比我要好一些，大约是200MBytes/s。这个速度还是让人觉得不够快，毕竟是内存操作。

 <span id="more-497"></span>
<p>我在stackoverflow上提了问：[http://stackoverflow.com/questions/7903013/the-speed-of-socket-is-120mb-s-on-a-computer-is-it-normal](http://stackoverflow.com/questions/7903013/the-speed-of-socket-is-120mb-s-on-a-computer-is-it-normal)

其中一位牛人的测试结果是：

> Read 25,586,204,672 bytes, speed: 5,245 MB/s 
> Read 53,219,426,304 bytes, speed: 5,317 MB/s 
> Read 85,018,968,064 bytes, speed: 5,416 MB/s 
> Read 117,786,968,064 bytes, speed: 5,476 MB/s

你没有看错，5GBytes/s，是我的30倍。他贴出了自己的电脑配置：

> I am using an ASUS Maximus IV, i7 2700K at 4.6 GHz, 16 GB of 1600 MHz DDR3, OCZ Vertex 3 240GB SSD, with Centos 5.7.

这配置真让我羡慕：顶级的CPU、内存，还有固态硬盘！

注：今天终于了解了内存后面的667是什么意思，原来是频率，即667MHz。它与内存带宽的关系是：

> 内存带宽=频率*内存位宽/8

如果是双通道内存，则位宽为128，单通道为64。所以DDR2 667的带宽为：667*128/8=10672MBytes=10.4GBytes

但这里引申出来一个问题：DDR2 667的内存的带宽已经这么大，够用了，那么我换成更高频率的内存，CPU不变，对我的程序速度是不是没有任何帮助？

自如说内存的频率跟CPU是配套的，电脑城装电脑的小弟们才需要知道这些。我觉得我还应该补补这方面的课，因为我对主频外频什么的，一直都不太明白。
