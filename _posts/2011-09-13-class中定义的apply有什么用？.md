---
layout: post
title: class中定义的apply有什么用？
tags:
  - Scala
date: 2011-09-13 22:35:30
---

我们知道，在object中定义apply方法后，我们可以把()操作，转化为apply函数，如：
<pre class="csharpcode"><span class="kwrd">object</span> test {
    def apply(s:String) = s*2
}
```
<p>则test(&#8220;a&#8221;)实际上是test.apply(&#8220;a&#8221;)

那么，下面这段代码呢？
<pre class="csharpcode"><span class="kwrd">class</span> A {
    def apply(s:String) = s*2
}
```
<p>虽然我们不能直接A(&#8220;a&#8221;)，但是我们可以：
<pre class="csharpcode">val a = <span class="kwrd">new</span> A
a(<span class="str">"a"</span>)
```
<style type="text/css">.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }
</style>
<p>即，将实例的()操作，转化为apply。