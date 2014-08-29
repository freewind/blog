---
layout: post
title: 1.5 Body parsers
tags: Java
date: 2013-01-01 16:52:13
---

(本文译者是子青)

原文：[http://www.playframework.org/documentation/2.0.2/JavaBodyParsers](http://www.playframework.org/documentation/2.0.2/JavaBodyParsers)

### Body parsers

什么是body parser?

HTTP请求（至少是使用POST和PUT操作的http请求）包含正文。正文可以被HTTP头Content-Type指定的任意格式格式化。一个body parser转变这个请求正文为Java 值。

注解：你不能直接在Java 中实例化`BodyParser`，因为Play 中BodyParser必须使用`Iteratee[Array[Byte], A]`来处理增长性的body 内容，这必须使用Scala实例化。

然而Play 提供默认的`BodyParsers`，他能适应大多数用例(解析 Json,Xml,Text,上传文件)。然后你可以直接在Java中重用这些默认的parsers来定制自己的parsers;比如你能在Text基础上提供一个RDF parsers。

#### `BodyParser`的Java API

在Java API中，所有的body parsers必须生成一个`play.mvc.Http.RequestBody`值。这个通过body parser 计算的值,随后可以使用`request().body()`方法获取：

    public static Result index() {
      RequestBody body = request().body();
      return ok("Got body: " + body);
    }

    你可以对action指定`BodyParser`，采用`@BodyParser.Of`注解的方式：

    @BodyParser.Of(BodyParser.Json.class)
    public static Result index() {
      RequestBody body = request().body();
      return ok("Got json: " + body.asJson());
    }

    #### `Http.RequestBody`API

    所有Java API里面的body parsers 返回一个`play.mvc.Http.RequestBody`值。检索这个body Ojbect可以取得最合适的Java类型的请求正文。

    批注:用来解析请求正文的parser不支持这个content类型,RequestBody中的方法（如`asText()`或者`asJson()`)将返回空。比如在action方法上加入`@BodyParser.Of(BodyParser.Json.class)`注解，在这个方法生成的body上调用`asXml()`将返回null。

    某些parsers能提供指定的类型而不只是`Http.RequestBody`（ie. Http.RequestBody的子类)。你可以使用`as(...)`助手方法自动将请求body转换成别的类型：

    @BodyParser.Of(BodyLengthParser.class)
    pulic static Result index() {
      BodyLength body = request().body().as(BodyLength.class);
      ok("Request body length: " + body.getLength());
    }

    #### 默认的body parser：`AnyContent`

    如果不自己指定body parser，Play将使用默认的与请求头中`Content-Type`最接近的parser类型：

    > text/plain: `String`, 通过 `asText()` 取得该类型。 application/json: `JsonNode`, 通过 `asJson()`取得该类型。 text/xml: `org.w3c.Document`, 通过 `asXml()`取得该类型 application/form-url-encoded: `Map<String, String[]>`, 对应 `asFormUrlEncoded()` multipart/form-data: `Http.MultipartFormData`, 对应 `asMultipartFormData()` 其它content类型: `Http.RawBuffer`, 对应`asRaw()`

    例子：

    pulic static Result save() {
      RequestBody body = request().body();
      String textBody = body.asText();

      if(textBody != null) {
        ok("Got: " + text);
      } else {
        badRequest("Expecting text/plain request body");
      }
    }

    #### 最大正文长度

    基于text的body parsers(比如text,json,xml或者formUrlEncoded)使用了最大content长度限制，因为parsers需要将所有的正文加载到内存中。

    默认的content长度为100KB

    要点：默认的正文长度可以在`application.conf`中定义：

    parsers.text.maxLength=128K

    你也可以通过`@BodyParser.Of`注解的方式指定最大的content长度：

    // Accept only 10KB of data.
    @BodyParser.Of(value = BodyParser.Text.class, maxLength = 10 * 1024)
    pulic static Result index() {
      if(request().body().isMaxSizeExceeded()) {
        return badRequest("Too much data!");
      } else {
        ok("Got body: " + request().body().asText()); 
      }
    }
