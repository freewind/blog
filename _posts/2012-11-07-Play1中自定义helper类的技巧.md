title: Play1中自定义helper类的技巧
tags:
  - PlayFramework1
date: 2012-11-07 13:07:10
---

大家都知道，Play1的模板使用了groovy作为模板语言，并且不允许在模板中直接创建方法。这个限制非常不方便，因为我们经常需要在在页面中创建一些临时使用的工具方法。

Play给出了两种方案：

1.  定义自己的JavaExtensions类
2.  在模板中以全路径方式调用方法，如"org.apache.commons.StringUtils.trimToEmpty(mystr)"

这两种方法都不够轻量，这里介绍一种更方便的方法：在action中创建一个匿名的工具类实例，传给模板

<pre class="csharpcode"><span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> show() {
    Object h = <span class="kwrd">new</span> Object() {
        <span class="kwrd">public</span> String trimToEmpty(Object obj) {
            <span class="kwrd">if</span>(obj==<span class="kwrd">null</span>) <span class="kwrd">return</span> <span class="str">""</span>;
            <span class="kwrd">return</span> obj.toString();
        }
    }
    render(h);
}<style type="text/css">.csharpcode, .csharpcode pre
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
</style>```

在模板中就可以这样使用：

<pre class="csharpcode"><h1>Hello, ${h.trimToEmpty(name)};</h1><style type="text/css">.csharpcode, .csharpcode pre
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
</style>```

对于临时使用的工具方法，这种方式非常适合。如果想在本controller范围或全局范围使用，只需要在合适的地方，创建一个action，使用play.mvc.Before，把它事先放入renderArgs中即可。

<pre class="csharpcode">@Before
<span class="kwrd">static</span> <span class="kwrd">void</span> prepareHelper() {
    Helper helper = <span class="kwrd">new</span> Helper();
    renderArgs.put(<span class="str">"h"</span>, helper");
}```
<style type="text/css">
.csharpcode, .csharpcode pre
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
.csharpcode .lnum { color: #606060; }</style>