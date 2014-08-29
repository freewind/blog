---
layout: post
title: 使用groovy写play程序的监控脚本
tags: 其它语言
date: 2012-11-21 20:49:27
---

之前做过的一个网站又挂了，老师留言让我及时修复。一查看原来访问量过大，OutOfMemoryError了。于是想写一个脚本自动监测网站运行情况，不能访问时自动重启，这样我就省事了。

主要功能如下：

1.  每隔一分钟访问一次首页2.  如果有数据返回，检查它是否包含某些关键字3.  如果有误，则检查数据库是否运行正常4.  再重启play

由于服务器的环境限制得很严格，不能访问其它网站，很多常用工具都没法下载，用bash来写比较麻烦。用java写的话，有点繁琐，而且修改起来也不方便，还要编译。最后决定用groovy，毕竟跟java比较接近好入手。

脚本如下：

<div class="mycode">

#!/usr/bin/env groovy

import java.net.*

// settings     
INDEX_URL = "[http://localhost:9001/login"](http://localhost:9001/login")      
APP_ROOT = "/path/to/app/root"      
SERVER_PID_FILE = APP_ROOT + '/server.pid'      
PLAY_CMD = "play"      
KEYWORDS = ['name="password"']

println "Starting web monitor ..."

// check every 1min     
while (true) {      
    println ""      
    println "&#8212;&#8212;&#8212;&#8212;&#8212; checking &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;"      
    println ""      
    try {      
        if (readUrl()) {      
            println "Everything is fine"      
        } else {      
            println "There is something wrong"      
            restartDB()      
            restartPlay()      
        }      
    } catch (Exception e) {      
        println "    exception: " + e.toString().take(40)      
    }      
    println "waiting for 1 min"      
    Thread.sleep(1000 * 60);      
}

boolean readUrl() {     
    println "Read data from ${INDEX_URL}"

    def input = null;     
    try {      
        URL url = new URL(INDEX_URL)      
        def conn = url.openConnection()      
        conn.setConnectTimeout(5 * 1000) // 5s      
        conn.setReadTimeout(5 * 1000) // 5s, important      
        conn.connect();

        input = conn.getInputStream()     
        def content = input.getText("UTF-8")

        println "    response has ${content.length()} chars"

        for(String keyword: KEYWORDS) {     
            if(content.contains(keyword)) {      
                println "    response contains keyword ${keyword}, valid"      
                return true      
            }      
        }      
        println "    response doesn't contain phone or email, invalid"      
    } catch (Exception e) {      
        println "    exception: " + e.toString().take(40)      
        return false      
    } finally {      
        if (input != null) {      
            input.close()      
        }      
    }      
}

def restartPlay() {     
    println "Check play"      
    def pid = checkPlayIsRunning()      
    println "    pid: ${pid}"

    if (pid) {     
        println "    try to kill the play process: ${pid}"      
        execute("kill -9 ${pid}").waitFor()      
    }

    if (new File(SERVER_PID_FILE).isFile()) {     
        println "    delete: ${SERVER_PID_FILE}"      
        new File(SERVER_PID_FILE).delete()      
    }

    println "    start play ..."     
    execute("${PLAY_CMD} start ${APP_ROOT} -%prod")      
}

def restartDB() {     
    println "Check DB"      
    println "    check pg_ctl status"      
    def proc = execute("su - postgres -c 'pg_ctl status'")      
    proc.waitFor()

    def out = proc.in.text     
    println "    response: ${out?.take(40)}"      
    if (!out.contains("server is running")) {      
        println "    not running, pg_ctl start ..."      
        execute("ru - postgres -c 'pg_ctl start'")      
    }      
}

String checkPlayIsRunning() {     
    println "    check ps of play"      
    def proc = execute("ps -eaf | grep play")      
    proc.waitFor()

    def line = proc.in.text.readLines().find { it.contains(APP_ROOT) }     
    println "    matched line: ${line?.take(40)}"

    return line?.split("\\s+")?.getAt(1)     
}

def execute(String command) {     
    ["sh", "-c", command].execute()      
}

</p></div>

该脚本运行起来后，就可以24小时不间断地监测网站了，我再也不用担心在睡梦中接到老师的问候电话了。

对了别忘了先安装groovy，还要把该脚本设为可执行。

如果把该脚本设为开机自动启动，则服务器重启也不怕了。如何设置，可参考我另一个文章：[如何让linux开机就运行某个groovy脚本](http://freewind.me/blog/20121121/1114.html)

<font color="#ff0000">注意：上面的脚本已经修改，增强了read timeout，这个值非常重要。因为在运行时发现，如果网站卡住（可以连接，但不返回数据时），此脚本也会卡住。</font>

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
