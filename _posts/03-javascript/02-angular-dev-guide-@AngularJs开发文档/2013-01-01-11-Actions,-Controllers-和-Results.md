---
layout: post
title: "1.1 Actions, Controllers 和 Results"
tags: Java
date: 2013-01-01 16:57:11
---

(本文译者是子青)

原文地址：[http://www.playframework.org/documentation/2.0.2/JavaActions](http://www.playframework.org/documentation/2.0.2/JavaActions)

### 什么是Action?

Play程序接收到的大部分请求，都是由`Action`来处理的。

一个Action基本上就是一个Java方法，它用于解析请求参数，产生结果数据，并发送给客户端。

    public static Result index() {
      return ok("Got request " + request() + "!");
    }

    一个action返回一个`play.mvc.Result`值，用来表示向web客户端发送的回复。在本例中，`ok`方法构造了一个**200 OK**的回复，并且包含了**text/plain**类型的回复内容。

    ### Controllers

    一个controller是指继承了`play.mvc.Controller`的类，它将多个action方法组织在一起。

    用于定义一个action的最简单的语法是一个无参数的static方法，并且返回一个`Result`类型的值。

    public static Result index() {
      return ok("It works!");
    }

    一个Action方法也可以包含参数：

    public static Result index(String name) {
      return ok("Hello" + name);
    }

    这些参数将由`Router`处理，它将从request url中取值，并传给action函数。参数的值可以从URL path或者URL的query string中取得。(TODO: 例子，什么是URL path和query string）

    ### Results

    让我们从一个简单的results开始：一个包含了状态码（如200,404,500），一组HTTP头信息和正文的，将发送给web客户端的result.

    这些results由`play.mvc.Result`定义, 并且`play.mvc.Results`类提供了几个助手方法可产生标准的HTTP results，比如我们在前一节使用的`ok`方法：

    public static Result index() {
      return ok("Hello world!");
    }

    这里有一些例子，用来创建不同的results:

    Result ok = ok("Hello world!");
    Result notFound = notFound();
    Result pageNotFound = notFound("<h1>Page not found</h1>").as("text/html");
    Result badRequest = badRequest(views.html.form.render(formWithErrors));
    Result oops = internalServerError("Oops");
    Result anyStatus = status(488, "Strange response type");

    这些助手类都可以在`play.mvc.Results`类中找到。

    ### Redirects也是简单的results

    让浏览器跳转(Redirecting)到新URL也是一种简单的result。但是这些result没有正文。

    有几个助手方法可以用来创建redirect results:

    public static Result index() {
      return redirect("/user/home");
    }

    默认使用`303 SEE_OTHER`的回复类型，但你也可以指定状态码：

    public static Result index() {
      return temporaryRedirect("/user/home");
    }
