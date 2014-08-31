---
layout: post
id: 1267
alias: play2-1-4-session-and-flash-scopes
tags: Java
date: 2013-01-01 16:51:21
title: 1.4 Session and Flash scopes
---

(本文译者是子青)

原文:[http://www.playframework.org/documentation/2.0.2/JavaSessionFlash](http://www.playframework.org/documentation/2.0.2/JavaSessionFlash)

### 会话和Flash范围

#### Play的区别

如果要在跨多个HTTP的请求中保存数据，你可以将数据保存为Session或者Flash范围。保存在Session中的数据可以在整个user session中获得，在flash范围的数据只能在下一个请求时候获得。

Session和Flash 中的数据采用Cookies方式添加到之后的HTTP 请求对象中而不是保存在服务器上。也就是说数据量很小（最多4KB）只能用来保存字符串值。

Cookies 使用加密钥匙的方式来标记，所以客户无法修改cookie数据（或者是无效的)。Play session 不是作为缓存功能设计的。如果需要缓存数据到关联的特定session,你可以使用Play内置的缓存机制，特定user 通过在user session中保存唯一的ID来关联缓存数据。

session没有专门的超时，用户关闭浏览器时候即回话超时或到期。如果需要为特定的应用指定超时动作，只要保存时间戳到user Session对象然后在你应用需要的任何时候调用(eg. 最大的session区间，最大的等待区间，等等）。

#### 读取Session值

从HTTP请求中获得传入的Session：

    public static Result index() {
      String user = session("connected");
      if(user != null) {
        return ok("Hello " + user);
      } else {
        return unauthorized("Oops, you are not connected");
      }
    }

    #### 保存数据至Session

    Session是Cookie,也是HTTP头，play提供了一个助手方法保存session 值：

    public static Result index() {
      session("connected", "user@gmail.com");
      return ok("Welcome!");
    }

    同样方法，可以删除传入session中任意值:

    public static Result index() {
      session.remove("connected");
      return ok("Bye");
    }

    #### 废弃整个session

    如果想清除整个session，可以如下操作：

    public static Result index() {
      session().clear();
      return ok("Bye");
    }

    #### Flash scope

    Flash scope 和Session的工作方式很接近，但是有两点不一样：

    数据的缓存是针对单一的请求。

    Flash的缓存不被签名，用户可以修改它。

    重点：Flash scope只能被用来在简单非异步的应用中传输 success/error 信息。因为数据只是为下一个请求保存但不能确保在复杂web应用中的请求顺序，Flash scope受到竞争条件的约束。

    这里有几个使用Flash scope的例子：

    public static Result index() {
      String message = flash("success");
      if(message == null) {
        message = "Welcome!";
      }
      return ok(message);
    }

    public static Result save() {
      flash("success", "The item has been created");
      return redirect("/home");
    }
