---
layout: post
title: 04. Session的Flash使用
tags: Scala
date: 2013-01-01 16:59:41
---

(本文译者是爱国者)

#### Session和Flash的区别

如果你需要在几个Http请求之间保持数据，你可以将他们保存在Session或Flash内. 两者的区别是，保存在Session中的数据在整个用户会话的过程中一直有效，而保存在Flash中数据只在下一次请求中有效。

需要注意的是，Session和Flash中的数据都不是保存在服务器端的，而是通过cookie机制在几个Http请求之间来回传递。因此保存在Session或Flash中的数据量不宜过大，最好控制在4KB以内，并且只能保存字符串值。

当然，这些保存在cookie中的数据是经过数字签名的，因此你不需要担心数据被篡改.

> 译者：敏感数据尽量保存在服务器缓存或加密后传输

Session不应该被当做缓存来使用（尽管你可以这么做）。如果你需要缓存某些数据，并且这些数据要跟Session绑定，你可以这样做：使用Play内置的缓存机制将需要缓存的数据保存起来，然后在Session中只保留一个和该缓存数据块关联的ID。

> Session中的数据不会超时失效，除非用户终止会话（比如浏览器关闭了）。会话终止后，session会被销毁。如果你需要给Session中某些数据设置超时的阀值，你可以为它们添加一个时间戳，然后在每次读取数据之前和当前时间进行对比.

#### 从Session中读取数据

要从session中读取数据，首先你要从Http的请求对象（`play.api.mvc.Request`）获得一个Session对象（`play.api.mvc.Session`）. Session对象提供了数据读取操作函数；

    def index = Action { request =>
      request.session.get("connected").map { user =>
        Ok("Hello " + user)
      }.getOrElse {
        Unauthorized("Oops, you are not connected")
      }
    }

    如果`request`被声明为隐式参数，那么Session对象会自动从`request`获得。

    def index = Action { implicit request =>
      session.get("connected").map { user =>
        Ok("Hello " + user)
      }.getOrElse {
        Unauthorized("Oops, you are not connected")
      }
    }

    想了解scala的隐式转换机制，请参阅：

    #### 将数据保存在Session

    `play.api.mvc.SimpleResult`对象提供了`withSession`函数设置session。

    Ok("Welcome!").withSession(
      "connected" -> "user@gmail.com"
    )

    如果只是希望往session中添加数据，请使用下面的方法：

    Ok("Hello World!").withSession(
      session + ("saidHello" -> "yes")
    )

    如果想从session删除某些数据，可以这样做

    Ok("Theme reset!").withSession(
      session - "theme"
    )

    #### 丢弃整个session

    Ok("Bye").withNewSession

    #### Flash对象的使用

    Flash的使用方法和Session一样，但有两点不同：

*   保存在Flash中的数据只对下一次请求有效*   Flash也是通过cookie机制实现的，但Play不会对Flash中的数据进行数字签名。因此客户端可以修改Flash中的数据
    > **重要提示:** Flash对象应该只用于非Ajax应用中来传输成功或失败的信息, 因为ajax应用的请求是异步的。

    下面是使用flash的例子：

    def index = Action { implicit request =>
      Ok {
        flash.get("success").getOrElse("Welcome!")
      }
    }

    def save = Action {
      Redirect("/home").flashing(
        "success" -> "The item has been created"
      )
    }

    如果需要在views中获取Flash中的数据，那么将flash声明为implicit

    @()(implicit flash: Flash)
    ...
    @flash.get("success").getOrElse("noooz")
    ...

    如果出现'_could not find implicit value for parameter flash: play.api.mvc.Flash_'的错误，那是因为你没有在Action的闭包中奖request声明为implicit. 请将`request => `改成`implicit request =>`

    def index() = Action {   
      implicit request =>
        Ok(views.html.Application.index())
    }

原文：[https://github.com/playframework/Play20/wiki/ScalaSessionFlash](https://github.com/playframework/Play20/wiki/ScalaSessionFlash)
