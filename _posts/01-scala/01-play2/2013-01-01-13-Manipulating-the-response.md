---
layout: post
alias: play2-1-3-manipulating-the-response
tags: Java
date: 2013-01-01 16:50:42
title: 1.3 Manipulating the response
---

(本文译者是子青)

原文地址：[http://www.playframework.org/documentation/2.0.2/JavaResponse](http://www.playframework.org/documentation/2.0.2/JavaResponse)

### 处理响应

#### 更改默认的Content-Type

结果的内容类型可以通过java值（如body的Content-Type）来推导出来。

例如：

    Result textResult = ok("Hello World!");

    将自动设置请求头中`Content-Type`为`text/plain`,然而：

    Result jsonResult = ok(jerksonObject);

    将会设置请求头中`Content-Type`为`application/json`。

    当你需要改变的时候，则需要在结果上用`as(newContentType)`方法去创建一个不同`Content-Type`请求头的相似结果:

    Result htmlResult = ok("<h1>Hello World!</h1>").as("text/html");

    你也可以在http响应中设置content type：

    public static Result index() {
      response().setContentType("text/html");
      return ok("<h1>Hello World!</h1>");
    }

    #### 设置http响应头

    你可以添加(或更新)任意HTTP响应头:

    public static Result index() {
      response().setContentType("text/html");
      response().setHeader(CACHE_CONTROL, "max-age=3600");
      response().setHeader(ETAG, "xxx");
      return ok("<h1>Hello World!</h1>");
    }

    注意：setting一个HTTP头将自动舍去先前设置的任意值。

    #### 设置或废弃cookies

    Cookies，在http头中为特殊格式，但Play提供了一系列助手类去简化。

    你可以为HTTP响应添加一个Cookie：

    response().setCookie("theme", "blue");

    废弃刚才保存在浏览器上的Cookie:

    response().discardCookies("theme");

    #### 为文本结果指定字符编码

    为基于文本格式的HTTP响应采用正确的字符编码进行处理很重要。Play使用`utf-8`为默认的字符编码。

    编码用来转化文本响应为对应的字节后通过网络套接字通信传输，并扩展请求头`Content-Type`加入`chartset=xxx`

    产生`Reuslt`值的时候可以指定编码方式:

    public static Result index() {
      response().setContentType("text/html; charset=iso-8859-1");
      return ok("<h1>Hello World!</h1>", "iso-8859-1");
    }
