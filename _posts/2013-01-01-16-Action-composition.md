---
layout: post
title: 1.6 Action composition
tags:
  - Java
date: 2013-01-01 16:52:49
---

(本文译者是子青)

原文:[http://www.playframework.org/documentation/2.0.2/JavaActionsComposition](http://www.playframework.org/documentation/2.0.2/JavaActionsComposition)

### Action 组件

这章介绍定义action接口功能的多种方式。

#### actions的提醒

前面，我们将action描述为返回`play.mvc.Result`类型值的Java 方法。实际上，Play管理acionts的内部原理如同函数。因为Java不支持第一级函数,一个由Java API提供的action 就是一个`play.mvc.Action`实例：

    public abstract class Action {

      public abstract Result call(Http.Context ctx);    

    }

    Play 创建一个根action来调用合适的action方法，这对于复杂的action组件也适用。

    #### 构造 actions

    你可以使用另一个类`play.mvc.Action`加上`@With`注解来构造action方法提供的代码

    @With(VerboseAction.class)
    public static Result index() {
      return ok("It works!");
    }

    `VerboseAction`的定义如下：

    public class VerboseAction extends Action.Simple {

      public Result call(Http.Context ctx) throws Throwable {
        Logger.info("Calling action for " + ctx);
        return delegate.call(ctx);
      }
    }

    在某个位置需要使用`delegate.call(...)`方式对包装的action授权。

    你也可以使用多个actions：

    @With({Authenticated.class, Cached.class})
    public static Result index() {
      return ok("It works!");
    }

    <span style="background:#FFF7D6"> 注解：`play.mvc.Security.Authenticated`和`play.cache.Cached` annotations和对应已经定义的Actions是一起推出的。TODO:在相关的API文档中查看更多信息。</span>

    #### 定制action annotations

    你也可以采用自定义的annotation来标记action组件，它必须使用`@With`对自身进行注解。

    @With(VerboseAction.class)
    @Target({ElementType.TYPE, ElementType.METHOD})
    @Retention(RetentionPolicy.RUNTIME)
    public @interface Verbose {
       boolean value() default true;
    }

    然后你可以在action方法上使用这个新注解:

    @Verbose(false)
    public static Result index() {
      return ok("It works!");
    }

    你的Action定义将如同获得配置文件一样检索注解：

    public class VerboseAction extends Action<Verbose> {

      public Result call(Http.Context ctx) {
        if(configuration.value) {
          Logger.info("Calling action for " + ctx);  
        }
        return delegate.call(ctx);
      }
    }

    #### Annotating 控制器

    你也可以在`Controller`类上直接加上action组件注解。这将应用到所有被这个controller定义的action方法。

    @Authenticated
    public Admin extends Controller {

      …

    }