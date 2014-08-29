---
layout: post
title: 09. Comet sockets
tags:
  - Scala
date: 2013-01-01 17:02:32
---

(本文译者是爱国者)

### Comet sockets

#### 使用chunked response创建comet sockets

**chunked response**的一个用途就是创建Comet sockets。 一个Comet socket就是一段只包含`<script>`元素的`text/html`文本。每次浏览器收到这一份chunked resposne后，会执行`<script>`里的脚本。我们可以利用这种特性向浏览器发送一些实时的事件(event)，或者说服务器能实时通知客户端(浏览器）某些事件已发生，让客户端（浏览器）对此做出响应，比如向服务器发送异步请求或局部刷新页面。

让我们举一个例子。这个例子使用`Enumerator`构造了三个chunked response。每个chuncked response都包含一个`<script>`标记块，`<script>`块中调用一个Javascript函数`console.log()`往浏览器控制台输出一行日志。

    def comet = Action {
      val events = Enumerator(
         """<script>console.log('kiki')</script>""",
         """<script>console.log('foo')</script>""",
         """<script>console.log('bar')</script>"""
      )
      Ok.stream(events >>> Enumerator.eof).as(HTML)
    }

    浏览器访问这个action，你会在浏览器控台看到三行日志

    > **提示:** `events >>> Enumerator.eof`是`events.andThen(Enumerator.eof)`另一个等价的写法。

    可以换一种更好的实现方式: 使用`play.api.libs.iteratee.Enumeratee`适配器将`Enumerator[A]`转换成`Enumerator[B]`。将上述的例子重构一下：

    import play.api.templates.Html

    // Transform a String message into an Html script tag
    val toCometMessage = Enumeratee.map[String] { data => 
        Html("""<script>console.log('""" + data + """')</script>""")
    }

    def comet = Action {
      val events = Enumerator("kiki", "foo", "bar")
      Ok.stream(events >>> Enumerator.eof &> toCometMessage)
    }
    > **提示:** `events >>> Enumerator.eof &> toCometMessage`是`events.andThen(Enumerator.eof).through(toCometMessage)`另一个中等价写法。

    #### 使用`play.api.libs.Comet`工具类

    我们提供了一个Comet工具类来处理这些comet chunked streams, 所做的工作跟上述的例子一样。

    > **注意:** 实际上这个工具类会为我们完成更多的工作，比如会针对浏览器兼容性将一段初始空白的缓存数据块推送给浏览器， 并且它同时支持JSON和String两种消息。它也可以通过类型类扩展，以支持更多的消息类型。

    用它来重写之前的例子：

    def comet = Action {
      val events = Enumerator("kiki", "foo", "bar")
      Ok.stream(events &> Comet(callback = "console.log"))
    }
    > **提示:** `Enumerator.callbackEnumerator`和`Enumerator.pushEnumerator`是以命令式风格创建灵敏的非阻塞迭代器的两种写法

    #### 恒久常新的iframe技术

    编写Comet socket的标准做法是，在HTML页面中放置一个iframe子页面。iframe向服务器发起一个Http请求。然后服务器连续不断地向客户端（浏览器）发送chunked comet response。另一方面，在HTML页面上定义一个回调函数。当客户端收到一个chunked comet response后，执行回调函数.

    举例：

    def comet = Action {
      val events = Enumerator("kiki", "foo", "bar")
      Ok.stream(events &> Comet(callback = "parent.cometMessage"))
    }

    以及相应的Html页面：

    <script type="text/javascript">
      var cometMessage = function(event) {
        console.log('Received event: ' + event)
      }
    </script>

    <iframe src="/comet"></iframe>

原文： [https://github.com/playframework/Play20/wiki/ScalaComet](https://github.com/playframework/Play20/wiki/ScalaComet)