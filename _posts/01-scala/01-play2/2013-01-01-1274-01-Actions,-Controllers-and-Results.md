---
layout: post
id: 1274
alias: play2-01-actions-controllers-results
tags: Scala
date: 2013-01-01 16:56:59
title: 01. Actions, Controllers and Results
---

(本文译者是爱国者)

英文文档：[https://github.com/playframework/Play20/wiki/ScalaHome](https://github.com/playframework/Play20/wiki/ScalaHome)

#### 什么是Action?

Play应用程序接受的大部分http请求都由一个Action处理。一个Action就是形如`(play.api.mvc.Rquest => play.api.mvc.Result)`的函数，其中`play.api.mvc.Request`代表一个Http请求，Action处理http请求，然后生成一个Http响应返回给客户端，宝石Http响应用`play.api.mvc.Result`表示. 一个典型的Action例子如下：

    val echo = Action { request =>
      Ok("Got request [" + request + "]")
    }

    这个例子中，`Ok`函数构造了一个**200 OK**的Http响应，并且在响应正文中包含**text/plain**文本.

    #### 创建Action

    `play.api.mvc.Action`伴随对象(companion object)提供了一些工具函数创建Action. 一个简单的应用如下： 

    Action {
      Ok("Hello world")
    }

    如果需要获取Http的请求对象 (即play.api.mvc.Request)，可以使用如下的方法：

    Action { request =>
      Ok("Got request [" + request + "]")
    }

    你也可以将`request`声明为**_implicit_**，这样可以方便其他API使用而无需显式引入（想了解implicit的用法，请参考)

    Action { implicit request =>
      Ok("Got request [" + request + "]")
    }

    最后一种创建Action的方法是指定一个`BodyParser`参数,比如:

    Action(parse.json) { implicit request =>
      Ok("Got request [" + request + "]")
    }

    关于`BodyParser`的用法在后面章节说明，如果你创建Action时不指定一个`BodyParser`,那么Play2会使用一个通用的body parser.

    #### 控制器是action的发生器(Controllers are action generators)

    控制器（Controller）就是生成一些`Action`对象的伴随对象。一个简单的Controller如下：

    package controllers

    import play.api.mvc._

    object Application extends Controller {

      def index = Action {
        Ok("It works!")
      }

    }

    在这个例子中，我们定义了一个叫`Application`的Controller，它有一个action生成函数`index`. 当然,Action生成函数可以带参数，这些参数可以被Action闭包捕获，例如：

    def hello(name: String) = Action {
      Ok("Hello " + name)
    }

    #### 创建简单的响应对象

    我们知道，Http响应由响应码、Http响应头和消息正文三部分组成。在Play 2.0中，一个简单的Http响应对象由`play.api.mvc.SimpleResult`类表示，比如：

    def index = Action {
      SimpleResult(
        header = ResponseHeader(200, Map(CONTENT_TYPE -> "text/plain")), 
        body = Enumerator("Hello world!")
      )
    }

    当然，我们使用一些工具类来简化上面的代码：

    def index = Action {
      Ok("Hello world!")
    }

    以下是一些用于创建常见Http响应的工具函数

    val ok = Ok("Hello world!")
    val notFound = NotFound
    val pageNotFound = NotFound(<h1>Page not found</h1>)
    val badRequest = BadRequest(views.html.form(formWithErrors))
    val oops = InternalServerError("Oops")
    val anyStatus = Status(488)("Strange response type")

    你可以在`play.api.mvc.Results`的特质(trait)和伴随对象中找到更多工具函数

    #### 重定向也是简单的`play.api.mvc.Result`

    重定向响应也是Http响应的一种，只不过没有消息正文。Play 2.0提供了几种工具函数创建重定向响应.

    def index = Action {
      Redirect("/user/home")
    }

    `Redirect`工具函数默认采用`303 SEE_OTHER`响应类型，但你可以根据实际情况设置其他重定向状态码, 比如：

    def index = Action {
      Redirect("/user/home", status = MOVED_PERMANENTLY)
    }

    #### 创建空白网页

    如果你还没想好这个Action该如何实现，或暂时无法实现，可以使用TODO函数创建一个空白网页。调用TODO函数会返回一个标准的'Not implemented yet'类型的响应。

    def index(name:String) = TODO

原文：[https://github.com/playframework/Play20/wiki/ScalaActions](https://github.com/playframework/Play20/wiki/ScalaActions)
