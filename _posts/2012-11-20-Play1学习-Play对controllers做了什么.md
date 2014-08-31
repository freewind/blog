---
layout: post
alias: what-has-play1-done-to-controllers
tags: PlayFramework1
date: 2012-11-20 15:44:07
title: Play1学习: Play对controllers做了什么
---

开始学习play框架，本篇作为一个引子。

Play的一大特色是在编译期使用javassist对字节码进行增强，增加很多对用户透明的代码，以达到更简洁的使用效果。以controller为例，有一些事情在play中可以做到，而在普通的java中很难做到。

看下面这段代码：

<pre class="csharpcode">package controllers;

import play.*;
import play.mvc.*;
import java.util.*;
import models.*;

<span class="kwrd">public</span> <span class="kwrd">class</span> Application extends Controller {

    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> index() {
        render();
    }

    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> hello(String name) {
        <span class="kwrd">if</span>(name==<span class="kwrd">null</span> || name.trim().length()==0) {
            index();
        }
        String message = <span class="str">"welcome!"</span>;
        render(name, message);
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

在这段代码里，play可以通过代码增强，做到以下事情：

1.  在hello这个action中，自动从request提交的参数中，找到名为name的key，将其值赋给参数name
2.  在hello中，当name为null或空时，直接调用index()跳转到index，而下面的render(name)不会被执行
3.  render(name)在把name的值传到模板的同时，也会以某种方式把'name'这个名传过去

这几个功能可以让我们的代码更简化一点，并且它们能过常规的方法（比如反射等），是难以做到的。如果把这个hello翻译为springmvc代码，大约是这样的：

<pre class="csharpcode">    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> hello(@Param(<span class="str">"name"</span>) String name) {
        <span class="kwrd">if</span>(name==<span class="kwrd">null</span> || name.trim().length()==0) {
            redirectTo(<span class="str">"/application/index"</span>);
            <span class="kwrd">return</span>;
        }
        String message = <span class="str">"welcome!"</span>;
        Map<String, String> data = <span class="kwrd">new</span> HashMap<String,String>();
        data.put(<span class="str">"message"</span>, message);
        render(data);
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

以上的代码为伪代码，因为我已记不清具体怎么写，不过不影响理解。可以看到对于第1点，需要一个注解并指定参数名为"name"，第2点需要增加一个以字符串的形式写上"index"，丧失了typesafe，不能利用重构及编译期查错，第3点需要手动指定参数名为name。

看起来是很小的改进，不过controller如此常用，这些小小麻烦累积起来，也会让人心情不爽。相比起来，play的代码更加简洁清晰，同时如果不注意，甚至没有意识到play在后面做了手脚。

Play到底对controller做了什么呢？我们可以通地反编译工具，将Application.class反编译为java代码，一目了解。这里推荐一个叫jd-gui的工具：[http://java.decompiler.free.fr/?q=jdgui](http://java.decompiler.free.fr/?q=jdgui)

下面是反编译之后的java代码：

<pre class="csharpcode">package controllers;

import play.classloading.enhancers.ControllersEnhancer.ControllerInstrumentation;
import play.classloading.enhancers.LocalvariablesNamesEnhancer.LocalVariablesNamesTracer;
import play.mvc.Controller;

<span class="kwrd">public</span> <span class="kwrd">class</span> Application extends Controller {
    <span class="kwrd">public</span> <span class="kwrd">static</span> String[] $index0 = <span class="kwrd">new</span> String[0];
    <span class="kwrd">public</span> <span class="kwrd">static</span> String[] $hello1195259493 = {<span class="str">"name"</span>};

    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> index() {
        Object localObject1;
        <span class="kwrd">try</span> {
            LocalvariablesNamesEnhancer.LocalVariablesNamesTracer.enter();
            <span class="kwrd">if</span> (!ControllersEnhancer.ControllerInstrumentation.isActionCallAllowed()) {
                Controller.redirect(<span class="str">"controllers.Application.index"</span>, <span class="kwrd">new</span> Object[0]);
            } <span class="kwrd">else</span> {
                ControllersEnhancer.ControllerInstrumentation.stopActionCall();
                render(<span class="kwrd">new</span> Object[0]);
            }
        } <span class="kwrd">finally</span> {
            localObject1 = <span class="kwrd">null</span>;
            LocalvariablesNamesEnhancer.LocalVariablesNamesTracer.exit();
        }
    }

    <span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> hello(String name) {
        Object localObject1;
        <span class="kwrd">try</span> {
            LocalvariablesNamesEnhancer.LocalVariablesNamesTracer.enter();
            LocalvariablesNamesEnhancer.LocalVariablesNamesTracer.addVariable(<span class="str">"name"</span>, name);
            <span class="kwrd">if</span> (!ControllersEnhancer.ControllerInstrumentation.isActionCallAllowed()) {
                Controller.redirect(<span class="str">"controllers.Application.hello"</span>, <span class="kwrd">new</span> Object[]{name});
            } <span class="kwrd">else</span> {
                ControllersEnhancer.ControllerInstrumentation.stopActionCall();
                <span class="kwrd">if</span> ((name == <span class="kwrd">null</span>) || (name.trim().length() == 0)) {
                    index();
                }
                String message = <span class="str">"welcome!"</span>;
                LocalvariablesNamesEnhancer.LocalVariablesNamesTracer.addVariable(<span class="str">"message"</span>, message);
                render(<span class="kwrd">new</span> Object[]{name, message});
            }
        } <span class="kwrd">finally</span> {
            localObject1 = <span class="kwrd">null</span>;
            LocalvariablesNamesEnhancer.LocalVariablesNamesTracer.exit();
        }
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

比我们自己写的代码长了几倍，看来play的确做了不少事情。这里简单解读一下（因本人水平有限，可能有误，欢迎指正）：

**首先看到多了两个field:**

<pre class="csharpcode">    <span class="kwrd">public</span> <span class="kwrd">static</span> String[] $index0 = <span class="kwrd">new</span> String[0];
    <span class="kwrd">public</span> <span class="kwrd">static</span> String[] $hello1195259493 = {<span class="str">"name"</span>};```
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

它们的作用是记录下每一个action的参数名，以方便从request中取参数时，知道action有哪些参数。由于参数名的信息会在编译期被忽略，正常情况下是拿不到的。Play通过内嵌eclipse的javac并打开debug选项，保证在编译期各变量的原始名称不会改变（需求证）。不过这样的话，应该也可以通过method的反射取到名称，不一定非得建一些field保存。我估计是为了性能和方便性考虑。

其规则为 $ + method.name + hashCodeOfParameters(method)

如果方法没有参数，则后面直接加0。有参数的话，会根据参数类型计算出一个固定的hash值，以区分同名方法。

**然后是LocalvariablesNamesEnhancer**

它是用来把方法的参数以及各局部变量的名与值保存在一个map中。观察hello方法中的这几句：

<pre class="csharpcode">LocalvariablesNamesEnhancer.LocalVariablesNamesTracer.enter();
LocalvariablesNamesEnhancer.LocalVariablesNamesTracer.addVariable(<span class="str">"name"</span>, name);
LocalvariablesNamesEnhancer.LocalVariablesNamesTracer.addVariable(<span class="str">"message"</span>, message);
LocalvariablesNamesEnhancer.LocalVariablesNamesTracer.exit();```
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

它们把方法的参数以及各局部变量都保存起来，这样才可在向模板传值时忽略变量名。因为在render(...)方法可，可以根据传入的值的hashcode，找到其对应的名字，再传给模板层。

**最后是redirect：**

前面说到，调到一个action方法时，有可能会变成一个redirect。但这些action也应该可以当作普通的方法调用，是怎么做到的呢？看index中的代码：

<pre class="csharpcode"><span class="kwrd">if</span> (!ControllersEnhancer.ControllerInstrumentation.isActionCallAllowed()) {
     Controller.redirect(<span class="str">"controllers.Application.index"</span>, <span class="kwrd">new</span> Object[0]);
} <span class="kwrd">else</span> {
     ControllersEnhancer.ControllerInstrumentation.stopActionCall();
     render(<span class="kwrd">new</span> Object[0]);
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

可以看到，每个action被调用时，会先调用自己是被当成action还是普通java方法，被其它代码调用。如果是被另一个action调用，就会变成redirect，否则按正常的方法调用。

这里没有解释为什么在hello中调用index()的话，后面的代码不会再执行。这是因为每个render方法都会通过代码增强，抛出一个异常（需求证），这点在本例中没有体现。

以上代码中还有一个localObject1没有提到，因为我目前也不知道它有什么用，先放在这里，等以后补充。

除了这里提到的几点外，play还有很多类似的增强。如果希望能更好地使用play（以及避开某些因代码增强导致的陷阱），需要对这些多一些了解。在以后的学习中，我会陆续写一些笔记，感谢关注。
