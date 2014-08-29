---
layout: post
title: Android步步惊心 之 asyncTask.class.newInstance()报错（修正版）
tags: Android
date: 2012-10-31 20:18:33
---

自建了一个AsyncTask，内容不是重点，示例如下：

<pre class="csharpcode"><span class="kwrd">private</span> <span class="kwrd">class</span> MyTask extends AsyncTask<Void, Integer, Void> {
    @Override
    <span class="kwrd">protected</span> Void doInBackground(Void... <span class="kwrd">params</span>) {
        <span class="rem">// do something</span>
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
<p>在代码中，由于会取消并重建该Task，所以写了一个Helper方法：

<pre class="csharpcode">  <span class="kwrd">public</span> <span class="kwrd">static</span> <T extends AsyncTask> T createAsyncTask(T t, Class<T> cls) {
        <span class="kwrd">if</span> (t == <span class="kwrd">null</span> || t.isCancelled() || t.getStatus() == AsyncTask.Status.FINISHED) {
            <span class="kwrd">try</span> {
                <span class="kwrd">return</span> cls.newInstance();
            } <span class="kwrd">catch</span> (Exception e) {
                <span class="kwrd">throw</span> <span class="kwrd">new</span> RumtimeException(e);
            }
        }
        <span class="kwrd">return</span> t;
    }```

于是可在自己的代码中这么调用：

<pre class="csharpcode"><span class="kwrd">private</span> MyTask mytask;

<span class="kwrd">private</span> MyTask getOrCreateMyTask() {
    mytask = Helper.createAsyncTask(mytask);
    <span class="kwrd">return</span> mytask;
}```

运行时发现经常出错，报这个错：

    java.lang.InstantiationException: MyTask

错误位于return cls.newInstance()这一行。

经过反复测试，发现第一次调用这行代码，正常返回一个MyTask实例，第二次再调用时，就会报错。而且错误信息只能得到cls.newInstance()出错，再往里就是native代码，没法调了。

我开始以为是我的代码有问题，反复调试，查看AsyncTask的源代码，都没发现可疑之处。万般无奈之下，去掉了这个助手方法，而是直接写在getOrCreateMyTask()方法中：

<pre class="csharpcode"><span class="kwrd">private</span> AsyncTask getOrCreateMyTask() {
    <span class="kwrd">if</span> (mytask == <span class="kwrd">null</span> || mytask.isCancelled() || mytask.getStatus() == AsyncTask.Status.FINISHED) {
        mytask = <span class="kwrd">new</span> MyTask();
    }
    <span class="kwrd">return</span> mytask;
}```

不同之处在于，这次使用 new MyTask() 而不是 cls.newInstance()。再试，一切正常！再也没有出现那个错误了。

然后，我又把MyTask换成了一个普通的类，使用cls.newInstance()一切正常。

<strike>实在没想到，连cls.newInstance()这样基本的方法，在android中都有可能出错。</strike>

**修正：**

非常抱歉，这完全不是android的错，而是我的错。

之所以无法cls.newIntance()，是因为该MyTask是一个private inner类，在此情况下无法通过该方法创建。

可以把它改为：

<pre class="csharpcode"><span class="kwrd">private</span> <span class="kwrd">static</span> <span class="kwrd">class</span> MyTask extends AsyncTask<Void, Integer, Void> {
    <span class="kwrd">public</span> MyTask() {}
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

或者：

<pre class="csharpcode"><span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">class</span> MyTask extends AsyncTask<Void, Integer, Void> {
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

由于这段时间开发android应用，遇到了太多莫名其妙的错误，所以遇到与自己所知不同的问题时，会首先想到是android有bug。以后要更加谨慎一些。
