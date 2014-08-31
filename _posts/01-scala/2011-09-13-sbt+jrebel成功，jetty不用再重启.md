---
layout: post
alias: succesfully-applied-sbt-jrebel-and-jetty-is-needed-restart
tags: Scala
date: 2011-09-13 21:59:54
title: sbt+jrebel成功，jetty不用再重启
---

首先打消各位scala开发者的疑虑：jrebel对scala是免费的！大家只需要去申请一个licence即可（注意，如果gmail收不到，可试其它邮箱）：
<p>[http://sales.zeroturnaround.com/](http://sales.zeroturnaround.com/)
<p>下载并安装之后，修改sbt的启动文件(在windows下，通常是sbt.bat)，增加以下参数:
<pre class="csharpcode">-noverify -javaagent:E:/java/jrebel/jrebel.jar```
<p>如果启动sbt时，看到jrebel的提示信息，就说明jrebel已经启用了。还差一步：修改项目的配置文件，让jetty不再自动重启（否则jrebel就白忙活了）。

修改/project/build/config.scala，增加：
<pre class="csharpcode">override def scanDirectories = Nil```
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
<p>禁止jetty检查文件改变。

再使用sbt时，输入的命令变为：
<pre class="csharpcode">> sbt
> jetty-run
> ~compile```
<p>当我们修改了任一scala文件后，马上就能看到它被编译的信息，而jetty却没有重新载入。刷新页面看，就能看到改动过的效果了，每次修改都可以节省几秒钟。

该方法在sbt 0.7.x上测试通过
