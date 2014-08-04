title: 3.2 Common use cases
tags:
  - Java
date: 2013-01-01 16:54:30
---

(本文译者是辉)

#### 常见的模板使用方式

模板，功能比较简单，你可以安装你的想法组合。下面是几个一些常见的场景的例子.

#### Layout

我们来声明一个模板: views/main.scala.html ,把它作为一个主布局模板。

    @(title: String)(content: Html)
    <!DOCTYPE html>
    <html>
      <head>
        <title>@title</title>
      </head>
      <body>
        <section class="content">@content</section>
      </body>
    </html>

    你可以看到，这个模板有两个参数：一个标题和HTML内容块。现在，我们可以从另一个views/Application/index.scala.html 模板，使用它：

    @main(title = "Home") {

      <h1>Home page</h1>

    }

    注意: 你可以采用两种方式来传参数 @main(title = "Home") 或者 @main("Home").

    比如，有时你在第二页需要一个侧栏或面包屑的具体内容块。你可以声明一个额外的参数：

    @(title: String)(sidebar: Html)(content: Html)
    <!DOCTYPE html>
    <html>
      <head>
        <title>@title</title>
      </head>
      <body>
        <section class="content">@content</section>
        <section class="sidebar">@sidebar</section>
      </body>
    </html>

    在我们的“index”的模板使用，如下：

    @main("Home") {
      <h1>Sidebar</h1>

    } {
      <h1>Home page</h1>

    }

    另外，我们可以单独的声明：

    @sidebar = {
      <h1>Sidebar</h1>
    }

    @main("Home")(sidebar) {
      <h1>Home page</h1>

    }

    #### Tags <标签>(它们只是一些scala方法)

    我们在views/tags/notice.scala.html 中建立以个tag来显示一个消息

    @(level: String = "error")(body: (String) => Html)

    @level match {

      case "success" => {
        <p class="success">
          @body("green")
        </p>
      }

      case "warning" => {
        <p class="warning">
          @body("orange")
        </p>
      }

      case "error" => {
        <p class="error">
          @body("red")
        </p>
      }

    }

    现在让我们在另一个模板中使用它：

    @import tags._

    @notice("error") { color =>
      Oops, something is <span style="color:@color">wrong</span>
    }

    #### Includes(包括？)

    在这里没有什么特别。你可以调用任何其他你喜欢的模板（或实际定义的其他任何功能）：

    <h1>Home</h1>

    <div id="side">
      @common.sideBar()
    </div>

    #### 其他Scripts或Css的定义

    在scala模板中定义其他Scripts或css(和play1.x一样),你可以这样来定义：

    @(title: String, scripts: Html = Html(""))(content: Html)

    <!DOCTYPE html>

    <html>
        <head>
            <title>@title</title>
            <link rel="stylesheet" media="screen" href="@routes.Assets.at("stylesheets/main.css")">
            <link rel="shortcut icon" type="image/png" href="@routes.Assets.at("images/favicon.png")">
            <script src="@routes.Assets.at("javascripts/jquery-1.7.1.min.js")" type="text/javascript"></script>
            @scripts
        </head>
        <body>
            <div class="navbar navbar-fixed-top">
                <div class="navbar-inner">
                    <div class="container">
                        <a class="brand" href="#">Movies</a>
                    </div>
                </div>
            </div>
            <div class="container">
                @content
            </div>
        </body>
    </html>

    在扩展模板中，需要添加一个额外的脚本：

    @scripts = {
        <script type="text/javascript">alert("hello !");</script>
    }

    @main("Title",scripts){

       Html content here ...

    }

    在扩展模板，也可不必添加额外的脚本，就像：

    @main("Title"){

       Html content here ...

    }