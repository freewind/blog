---
layout: post
alias: play2-1-3-http-routing
tags: Java
date: 2013-01-01 16:50:04
title: 1.2 HTTP routing
---

(本文译者是子青)

原文地址：[http://www.playframework.org/documentation/2.0.2/JavaRouting](http://www.playframework.org/documentation/2.0.2/JavaRouting)

### 内嵌http路由（router)

router将每个http请求转换成action调用（也就是控制器类中一个static,public 方法)

HTTP请求可以作为MVC框架处理的一个事件。这个事件包含两个主要信息：

> 请求路径（eg.` /clients/1542`,`/photos/list`),包含了查询语句.
> HTTP方法(GET,POST,DELETE...)

Routes 定义在`conf/routes`文件中，需要编译，也就是可以在浏览器中看到route文件中的错误。

### routes文件的语法

`conf/routes`-router的配置文件。文件列举了所有程序用到的routes。每个route包含了HTTP方法和调用action方法所关联的URI样式

route定义如下：

    GET   /clients/:id          controllers.Clients.show(id: Long)

    注解：action调用 参数类型在参数名称的后面，同Scala。

    每个route以HTTP 方法开始,URI样式结束。最后的元素是调用说明。

    可以在route文件中加入注释，以#开头。

    # Display a client.
    GET   /clients/:id          controllers.Clients.show(id: Long)

    ### HTTP 方法

    HTTP方法可以是HTTP支持的任意有效方法 (`GET`,` POST`,` PUT`,` DELETE`, `HEAD`).

    ### URI样式

    URI样式定义了route的请求路径，请求路径有些部分可以是动态的。

    #### 静态路径

    如为了准确匹配`GET /clients/all/` 请求，可以这样定义route:

    GET   /clients              controllers.Clients.list()

    #### 动态部分

    如果想定义一个route,通过用户id取得用户信息，你需要加入动态部分：

    GET   /clients/:id          controllers.Clients.show(id: Long)

    注解：一个URI样式可以拥有多个动态部分

    动态部分默认的匹配策略被定义为正则表达式`[^/]+`，意思是任意定义如`:id`的动态部分将会准确匹配到一个URI路径。

    #### 动态部分跨越多个 /

    捕获被 / 分割的多个URI路径片段，你可以定义一个采用`*id`语法的动态部分,它运用了`.*`正则表达式语句。

    GET   /files/*name          controllers.Application.download(name)

    如`GET /files/images/logo.png`,`name`动态部分将捕获`images/logo.png`这个值

    #### 动态部分采用定制的正则表达式

    采用自己的正则表达式语句定义动态部分，采用`$id<regex> `语法：

    GET   /clients/$id<[0-9]+>  controllers.Clients.show(id: Long)

    #### action生成器方法的调用

    route定义的最后部分是call(调用）。该部分必须定义一个对于action方法有效的调用。

    如果是一个无参action方法，只需给定完全匹配的方法名称：

    GET   /                     controllers.Application.homePage()

    如果是有参action方法,将从请求URI里面寻找对应的参数，即从URI本身或者从查询语句中获得。

    # Extract the page parameter from the path.
    # i.e. http://myserver.com/index
    GET   /:page                controllers.Application.show(page)

    或者:

    # Extract the page parameter from the query string.
    # i.e. http://myserver.com/?page=index
    GET   /                     controllers.Application.show(page)

    匹配的`show`方法定义在`controllers.Application`控制器:

    public static Result show(String page) {
      String content = Page.getContentOf(page);
      response().setContentType("text/html");
      return ok(content);
    }

    #### 参数类型

    `String`类型的参数,参数类型可不填。如果想将传参转化为指定的Scala类型，可以加入明确的类型：

    GET   /client/:id           controllers.Clients.show(id: Long)

    然后在controller中为action方法应用对应参数类型

    public static Result show(Long id) {
      Client client = Client.findById(id);
      return ok(views.html.Client.show(client));
    }

    注解:参数类型的指定使用前缀。泛型类别的用`[]`标记而不是Java中的`<>`。如：`List[String]`等同于Java类型`List<String>`。

    #### 参数值固定

    有时候需要为参数指定固定值：

    # Extract the page parameter from the path, or fix the value for /
    GET   /                     controllers.Application.show(page = "home")
    GET   /:page                controllers.Application.show(page)

    #### 参数值默认

    在请求中没有为指定参数赋值的情况，可以为其提供默认值:

    # Pagination links, like /clients?page=3
    GET   /clients              controllers.Clients.list(page: Integer ?= 1)

    Routing优先级

    存在多个路由规则都可以匹配相同的请求的情况。如果有冲突，第一个路由规则(按照声明顺序)将被使用。

    #### 反向路由

    router可以以java调用方法的形式，来生成一个URL（比如在模板页面上<a herf='@{Business.index()}'></a>） 。这样所有的URI模式可以集中在单一的配置文件中，当你重构程序时候你会更自信。

    针对routes 文件中每个controller，router将会在`routes`包中生成一个逆向控制器，使用同样的action 方法，拥有相同的signature，但返回一个`play.mvc.Call`而不是`play.mvc.Result`。

    这个`play.mvc.Call`定义一个HTTP调用，并同时提供HTTP方法和URI。

    下面的例子创建了一个控制器：

    package controllers;

    import play.*;
    import play.mvc.*;

    public class Application extends Controller {

      public static Result hello(String name) {
          return ok("Hello " + name + "!");
      }

    }

    如果将此映射到`conf/routes`文件中：

    # Hello action
    GET   /hello/:name          controllers.Application.hello(name)

    通过使用`controllers.routes.Application`逆向控制器,你可以随后将URL逆向至`hello`action 方法：

    // Redirect to /hello/Bob
    public static Result index() {
        return redirect(controllers.routes.Application.hello("Bob")); 
    }

    ### Imports

    如果你厌倦经常去写`controller.Application`,可以如下采用定义默认import的方式

    val main = PlayProject(…).settings(
      routesImport += "controller._"
    )
