---
layout: post
id: 1108
alias: play-groovy-makes-playframework-closes-to-my-expectation
tags: PlayFramework1
date: 2012-11-16 23:36:35
title: play-groovy:离期望的playframework更近一步
---

这里所说的playframework是指play1，而不是play2。另外，本文中不考虑scala。

Play1是一个很有创造力的java web框架，在很多方面离我的期待都很接近了。它通过各种字节码增强技术，大大简化了各种操作，使用起来让人感觉非常舒适。

但它有一个无法克服的问题：必须使用java语言。这在很多时候都不太方便，比如缺少多行文本，字符串表达式，闭包等等，另外想在代码中直接拼json也很麻烦。

为了解决这些问题，曾经我尝试过xtend，因为它在eclipse的帮助下，直接生成java源代码，与play集成在理论上没问题。我们可以在其它的目录中写xtend代码，让java源文件生成到app目录下，让play直接读取，这样就不需要额外写xtend的插件了。可惜由于xtend的不成熟，以及生成的java代码与其它库的习惯不同（field前加了一个下划线），使用起来各种不方便。虽然我很看好xtend即将推出的active annotation功能，但在目前的情况下，基本上是无法使用的。

昨天突然看到了一个插件，可以让play支持groovy：[https://github.com/marekpiechut/play-groovy](https://github.com/marekpiechut/play-groovy)

该插件实现了groovy的编译器，并且处理好了hot-reload，使用起来跟java的感觉一致。目前只支持groovy 1.8，还不支持groovy 2.x

经过我的简单尝试，感觉比较满意：hot-reload, 出错报告，与java代码共存，ebean插件等，都运行良好。也许深入使用后，还会发现其它的一些问题，不过目前来说，感觉很满意。

我并没有专门去学习groovy语言，而是完全把它当作java来用，因为对于java的绝大部分语法，groovy都直接支持。看看我的groovy代码，基本上跟java一模一样：

<pre class="csharpcode"><span class="kwrd">public</span> <span class="kwrd">class</span> Application extends Controller {

    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> index() {
        List<Question> questions = Question.find.all();
        render(questions)
    }

    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> askQuestion() {
        render()
    }

    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> createQuestion(String title, String content) {
        Question question = <span class="kwrd">new</span> Question()
        question.title = title
        question.content = content
        question.save()
        index()
    }
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
<p>但我又可以在需要的时候，使用那些java没有提供的功能，比如：

<pre class="csharpcode">    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> text() {
        renderText(<span class="str">''</span><span class="str">'Hello, this
                    is multi-line
                    text'</span><span class="str">''</span>)
    }

    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> strExpression() {
        String name = <span class="str">"Freewind"</span>
        renderText(<span class="str">"Hello, this is ${name}"</span>)
    }

    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> html() {
        renderHtml(<span class="str">''</span><span class="str">'<h1>hello,html</h1>'</span><span class="str">''</span>)
    }

    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> smallJson() {
        def res = [hello: <span class="str">'world'</span>,
                iam: <span class="str">'freewind'</span>]
        renderJSON(res)
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
<style type="text/css">
<p>.csharpcode, .csharpcode pre
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
<p>还有我特别想要的拼复杂的json功能（如果前台使用一些js mvc框架，需要从后台取大量json数据）

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
<p>这个json builder的语法，是不是很简洁？里面甚至还可以有业务逻辑。

对于闭包之类的功能，可以在需要用的时候再去查文档。而上面这些简单而有用的功能，只需要看几眼文档就可以了，几乎没有学习成本。在编辑器的帮助下，如果我们按java的思路来写代码，错误检查、方法提示等功能，都可以运行的很好。

我觉得这是一种很划算的做法：用很小很小的学习成本，带来较大的便利。在通常的情况下，尽量使用java语法，但当某些时候觉得特别不方便时，再看看groovy中有没有提供什么语法糖，能简化我们的代码，让代码看起来更整洁。

 

写到这里，也许大家心里都有一个疑问：**性能**。

groovy是一种动态语言，性能要比java差很多。但我觉得这不是个大问题，毕竟groovy那边有个grails，这么多年不是运行的好好的吗？如果还是不放心，还有一个好消息：groovy 2.x同时支持静态类型。只需要在代码中增加一个@TypeChecked注解，就可以将静态调用按静态方式编译代码，几乎跟java的性能一样。等该插件支持groovy 2.x时，就可以放心地使用了。

等熟悉groovy后，还可以利用它的一些其它特性，进一步精简我们的代码。

我觉得这个插件特别适合于那些喜欢play，不满java与scala的朋友使用。相比scala，groovy不论是学习成本还是一java之间的结合都要好得多。目前该插件还比较简单，版本号仅为0.1，可以预见在实际使用过程中会遇到一些问题，希望有兴趣的朋友可以一些完善它。

参考资料：

1.  groovy 2.x新特性：[http://fr.slideshare.net/glaforge/groovy-20-webinar](http://fr.slideshare.net/glaforge/groovy-20-webinar)
