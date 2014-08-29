---
layout: post
title: Groovy的JsonBuilder还是不够groovy
tags: 其它语言
date: 2012-12-21 16:36:31
---

之前特别想把groovy的支持集成到play中，最主要的原因是看中了它的JsonBuilder。看下面这段代码：

<pre class="csharpcode">    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> json() {
        def builder = <span class="kwrd">new</span> groovy.json.JsonBuilder()
        def root = builder.people {
            person {
                firstName <span class="str">'Guillame'</span>
                lastName <span class="str">'Laforge'</span>
                <span class="kwrd">if</span> (1 == 0) {
                    address {
                        city <span class="str">'Paris'</span>
                        country <span class="str">'France'</span>
                        zip 12345
                    }
                }
                married <span class="kwrd">true</span>
                <span class="rem">// a list of values</span>
                conferences <span class="str">'JavaOne'</span>, <span class="str">'Gr8conf'</span>
            }
        }
        renderJSON(builder.toString())
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

这个JsonBuilder的结构我非常喜欢，简洁明了，最重要的是还可以在中间随意穿插自己的控制逻辑。

为什么我这么看重JsonBuilder，因为我使用了angularjs这样的前端框架，前后台经常需要用json通信。为了得到最好的性能，为每个页面定制json是非常必要的手段。但使用gson/jackson把bean转换为json，控制的粒度不够细；而在java代码中拼json，不用想就知道有多痛苦；而在play模板中拼json，除了要对每个值进行raw()的处理外，还需要处理分隔符，少不了这样的语句：

> <font style="background-color: #ffffff">${if var_isLast ? "" : ","}</font>

有够麻烦。

当我看到groovy的JsonBuilder的时候，我眼前一亮，这东西太好了。

（后来发现不需要专门集成groovy，因为play的模板就是基于groovy的，我们可以把这个builder直接写在模板里，用 %{}%括起来就行了。这是另一个问题，不在本文中说了）

然而现在却发现这个JsonBuilder还不够groovy，还不够好，因为如果想生成一个array形式的json，它的这种语法不支持。

比如我想生成一个这样的json:

<pre class="csharpcode">[
    {<span class="str">"code"</span>: <span class="str">"111"</span>, <span class="str">"value"</span>:<span class="str">"222"</span>},
    {<span class="str">"code"</span>: <span class="str">"333"</span>, <span class="str">"value"</span>:<span class="str">"444"</span>}
]```
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
<p>JsonBuilder没办法按前面的语法来写，因为它只能生成object形式的，比如例中的那样，或者下面这样的：

> {
> 
>     "code" : "111", 
> 
>     "value" : "222" 
> 
> }
> 
>  

那么JsonBuilder是否能生成array形式的json呢？可以是可以，但语法形式就不一样了。如下例：

<pre class="csharpcode">def list = [
    [code: <span class="str">"111"</span>, <span class="kwrd">value</span>: <span class="str">"222"</span>],
    [code: <span class="str">"333"</span>, <span class="kwrd">value</span>: <span class="str">"444"</span>]
]

builder = <span class="kwrd">new</span> groovy.json.JsonBuilder(list)
println builder.toString()```
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
<p>可以看到，这已经是纯粹的groovy代码了。这时候要使用[]而不是{}，而且list和map都是用[]。

看一下这两种写法之间的不同：

1.  JsonBuilder的语法，使用{}，每行结果没有逗号，key与value之间没有冒号2.  使用groovy内置的集合语法时，使用[]，而且list和map都是[]，并且各元素之间都要有逗号

这里存在着一大坑：如果你写错了，比如多写了冒号或少写了逗号，它有可能不报错，但结果不同；也有可能报一个完全让人摸不着头脑的错误！

比如，

1.  如果用JsonBuilder的语法，在key与value之间多写了一个冒号，不会报错，只是那一项被忽略了2.  如果用集合的写法，但在list的元素之间，少写了行尾的逗号，会报一个NullPointerException!

这种混乱会让人在使用过程中极易犯错，而花费大量的调试时间。

就算我强制只使用object形式的json，对于array，就给它加上一个无意义的key，也不能完全避免以上混乱。假设我现在有如下的一个list:

> def list = [
> 
>         [code: 111, value: 222], 
> 
>         [code: 333, value: 444], 
> 
> ]
> 
>  

在生成json的时候，我要用它这个list，并且把它的每个值都加倍，应该怎么做呢？

<pre class="csharpcode">def builder = <span class="kwrd">new</span> groovy.json.JsonBuilder()
builder.system {
    name <span class="str">"mysystem"</span>
    settings list.collect {
        [
                code: it.code * 2,
                <span class="kwrd">value</span>: it.<span class="kwrd">value</span> * 2
        ]
    }
}
println builder.toPrettyString()```
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
<p>看到了吗？要使用collect方法对原list进行变换，它要把每个元素转换为一个map，即[]包裹起来的代码。在复杂的情况下，这实在让人难以接受。

综上所述，在play中引入groovy的JsonBuilder，带来的混乱要大于其方便性，所以决定抛弃它。如果抛弃了JsonBuilder，则集成groovy的意义也不大了，所以我打算不再考虑groovy。

现在想到的办法，还是对play进行扩展，增加以下功能：

1.  给list标签增加separator属性，方便增加json间的逗号2.  添加一个新的render方法，以字符串形式返回生成的模板内容，而不是抛出异常。以方便在模板中调用拿结果。3.  增加一个将object转换为json的扩展方法，返回Raw类型，方便在模板中调用

后两者可以在自己的代码中实现，而对于1，必须要修改play的源代码，因为list标签是写死在代码中(GroovyInlineTag)，没有办法对它进行扩展。
