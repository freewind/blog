---
layout: post
alias: why-array-1-2-array-1-2-is-false
tags: Scala
date: 2011-09-13 22:41:04
title: scala中==是比较内容，但为什么Array(1,2)==Array(1,2)为false
---

在scala中，==就相当于java中的equals，基于内容比较。
<p>比如:
<pre class="csharpcode">List(1,2,3) == List(1,2,3) // true```
<p>但是有一个例外：
<pre class="csharpcode">Array(1,2,3) == Array(1,2,3) // false```
<p>为什么Array与众不同？是因为java语言的限制，性能上的担心，还是故意设计的呢？

<span id="more-201"></span>
<p>在scala官网上有这个讨论： 

[http://www.scala-lang.org/node/3487](http://www.scala-lang.org/node/3487) 

但是对于英文不怎么样，同时对scala不怎么了解我们，还是看不太懂。（其中odersky是scala作者） 

大概意思是担心性能被拖后腿，ordersky的这段回话是重点： 

> Even Java seems to admit they got equals/hashCode wrong for Arrays. Would it be possible to use java.util.Arrays.equals and ju.Arrays.hashCode (note plural Arrays) instead of the inherited equals/hashCode from Object?
> 
> No, unfortunately, because arrays might masquerade as objects. So we could only do it with a run-time check, which would slow down all calls to equals and hashCode.

可惜的是，还是不太明白
