---
layout: post
title: 3.1 Templates syntax
tags:
  - Java
date: 2013-01-01 16:53:46
---

(本文译者是辉)

### 模板引擎

#### 模板建立在Scala基础之上

Play2.0 附带了新的、强大的基于Scala的模板引擎,其设计灵感来源于ASP.NET Razor。它的特性:

**简洁，易用，灵活：**在一个模板中最大限度的代码重用，编码快速，灵活。不像大多数模板语言，你不需要显示的引用其他HTML代码块，解析器能够自动解析到所需要的代码。使得这是一个简洁，易用，灵活的很好模板类型。

**易学习:**如果你有Html编码经验，可以使用简单的Scala结构，快速的进入模板开发。

**不是一个新的编程语言: **我们选择现有Scala语言，不去创建一个新的语言。我们希望开发者可以使用现有Scala语言，并提供了一个模板标记库，来进行Html编码工作

**易编辑的:**它并不需要特定的编辑工具，使您可以在任何文本编辑器中编辑。

**注：**模板引擎使用Scala语言,对于Java开发人员，并不是一个问题,你几乎可以把它作为Java语言。

切记,不要在模板中编写复杂的Scala代码。大多数的时候你可以从你的模型对象中访问数据,比如:

    myUser.getProfile().getUsername()

    模板是被编译过的,所以你能够在浏览器中看到错误提示: 图片

    指定参数类型使用一个后缀的语法如：@（title:String）。泛型类型指定使用[]，而不是Java中的< > 。例如,@（list:List[String]）,而不是List<String> list。

    #### 概述

    Play的一个Scala模板文件是一个简单的文本文件,其中包含小块的Scala代码。模板可以包含任何基于文本格式的内容,如HTML、XML或者CSV。

    模板系统对于那些习惯使用HTML的前端开发者是容易处理的。

    模板文件被编译成标准Scala函数,以下是简单的命名约定。如果您创建了一个views/application/index.scala.html模板文件,它将生成一个views.html.Application.index Class文件，该Class具有render()方法

    例如：

    @(customer: Customer, orders: List[Order])

    <h1>Welcome @customer.name!</h1>

    <ul> 
    @for(order <- orders) {
      <li>@order.getTitle()</li>
    } 
    </ul>

    然后，您可以在任何Java代码中调用，如:

    Content html = views.html.Application.index.render(customer, orders);

    #### @的用法

    Scala模板使用@作为单一特殊字符。每次遇到这个字符,它表明动态语句的开始。你不需要显式地关闭代码块-能从你的代码中推断出动态语句的结束:

    Hello @customer.getName()!
           ^^^^^^^^^^^^^^^^^^
              Dynamic code

    上边这个语法只支持简单的语句。如果你想要插入一个multi-token声明,需使用@（你的Code），如:

    Hello @(customer.getFirstName() + customer.getLastName())!
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 
                              Dynamic Code

    您还可以使用大括号，写一个multi-statement语句块，如：

    Hello @{val name = customer.getFirstName() + customer.getLastName(); name}!
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                      Dynamic Code

    因为@是一个特殊的字符,有时候你会需要使用一个@。使用“@@”:

    My email is bob@@example.com

    #### 模板参数

    一个模板就像一个函数,所以它可能需要参数,参数的声明必须在模板文件顶部,如：

    @(customer: models.Customer, orders: List[models.Order])

    您还可以给参数设定默认值:

    @(title: String = "Home")

    也可以声明多个参数:

    @(title:String)(body: Html)

    #### Iterating（迭代）

    <p>您可以使用for关键字，来进行迭代：</p>

    <ul>
    @for(p <- products) {
      <li>@p.getName() ($@p.getPrice())</li>
    } 
    </ul>

    #### If-blocks（if语句块）

    if语句块也很简单。用Scala的标准if语句:

    @if(items.isEmpty()) {
      <h1>Nothing to display</h1>
    } else {
      <h1>@items.size() items!</h1>
    }

    #### 声明可重复使用的语句块

    可重用的语句块的声明和使用例子：

    @display(product: models.Product) = {
      @product.getName() ($@product.getPrice())
    }

    <ul>
    @for(product <- products) {
      @display(product)
    }

    您还可以声明可重用的纯代码块:

    @title(text: String) = @{
      text.split(' ').map(_.capitalize).mkString(" ")
    }

    <h1>@title("hello world")</h1>

    **注:** 声明语句块这种方式，在一个模板尽量少使用,不推荐在一个模板中编写复杂的逻辑。通常将这些代码具体化到一个Java类中(你可以放在view/package下)。

    匿名可重用语句块的声明:

    @implicitFieldConstructor = @{ MyFieldConstructor() }

    #### 声明可重用的值

    您可以使用defining定义作用域内的值:

    @defining(user.getFirstName() + " " + user.getLastName()) { fullName =>
      <div>Hello @fullName</div>
    }

    #### Import statements (Import 用法)

    您可以在参数声明后导入任何你想要的模板（或子模板）：

    @(customer: models.Customer, orders: List[models.Order])

    @import utils._

    ...

    Import一个绝对路径,使用root前缀在import语句中。

    @import _root_.company.product.core._

    如果你有一些常用的Import,需要在所有的模板中应用,您可以在Project/ Build.scala中声明

    val main = PlayProject(…).settings(
      templatesImport += "com.abc.backend._"
    )

    #### Comments（注释）

    在模板中使用 @_ _@编写注释:

    @*********************
     * This is a comment *
     *********************@

    你可以把模板的第一行的注解记录到Scala API文档：

    @*************************************
     * Home page.                        *
     *                                   *
     * @param msg The message to display *
     *************************************@
    @(msg: String)

    <h1>@msg</h1>

    #### 转义

    默认情况下,动态内容部分会根据模板类型(例如。HTML或XML)规则来解析。如果你想在模板中输出一个原始内容片段。

    例如输出原始HTML:

    <p>
      @Html(article.content)    
    </p>