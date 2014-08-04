---
layout: post
title: Express + Mongoose 极简入门
tags:
  - JavaScript
date: 2012-05-07 21:38:09
---

今天尝试使用express + mongoose，构建了一个简单的Hello world，实现以下功能：

1.  定义mongodb使用的Schema，一个User
2.  访问/输出Hello world
3.  访问/init向mongodb插入初始化数据
4.  访问/users从mongodb中取数据，并以json方式发送到浏览器

各功能都是极简单的试用，没有用到复杂的功能，但也有一定的参考价值，希望对大家有所帮助。

本次使用到的模块如下：

1.  mongoose[http://mongoosejs.com/](http://mongoosejs.com/)一个在nodejs中使用的对mongodb进行建模的工具，可以定义一些Schema（定义每个doc里的字段名、类型、初始值、验证条件等），增删改查等，功能比较贴心。
2.  express[http://expressjs.com](http://expressjs.com)nodejs环境中的web framework，提供了比nodejs的原始api高一级的抽象，方便进行web编程，如router、request/response的处理等，有很多基于它的模块或框架
3.  express-mongoose[https://github.com/LearnBoost/express-mongoose](https://github.com/LearnBoost/express-mongoose)让express中的某些方法，如res.send等，支持mongoose返回的查询，减少嵌套

在开始之前，先在项目目录的根目录下安装各模块：

> <font style="background-color: #ffffff">npm install mongoose</font>
> 
> <font style="background-color: #ffffff">npm install express</font>
> 
> <font style="background-color: #ffffff">npm install express-mongoose</font><font style="background-color: #ffffff">       
> </font>

项目代码放在这里，可供参考：[https://github.com/freewind/express-mongoose-demo](https://github.com/freewind/express-mongoose-demo)

## 一、使用express创建一个Hello, world

创建一个app.js，内容如下：

> var express = require("express");
> 
> var app = express.createServer();
> 
> app.get('/', function(req, res) {     
> res.send('Hello, world');      
> });
> 
> app.listen(3000);

短短几行代码，创建了一个app server。它监听于端口3000，并且访问/时，会向客户端发送Hello world.

## 二、试用/

启动该程序：

> <font style="background-color: #ffffff">node app.js</font>

如果没有提示错误，说明启动成功。

打开浏览器，访问：[http://localhost:3000](http://localhost:3000)

显示如下：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb21.png "image")](http://freewind.me/wp-content/uploads/2012/05/image21.png)

## 三、定义Models

<p>创建一个models.js，用于定义程序中使用的model（主要是Schema）。内容如下：

</p>
> <p>var mongoose = require('mongoose');
> 
> var Schema = mongoose.Schema;
> 
> // Define User schema       
> var _User = new Schema({        
>     email : String,        
>     name : String,        
>     salt : String,        
>     password : String        
> });
> 
> // export them       
> exports.User = mongoose.model('User', _User);        
> 
> </p>

这里定义了两个Schema，一个User。为了简便好认，我在Schema前面加了一个下划线，当然你也可以使用其它命名，如UserSchema等。

在定义Schema时，还可以设一些默认值、验证什么的，但这里先忽略，毕竟是极简试用。

> mongoose.model('User', _User);

这一句中，第一个参数相当重要，它是给User这个schema，定义了一个名字。其它地方如果想使用这个model，可以这样：

> <font style="background-color: #ffffff">var User = mongoose.model('User')</font>

但在这里，我们直接把它放在exports里了，更方便：

> exports.User = mongoose.model('User', _User);

## 四、/init

现在要把models.js导入到app.js中，并且定义一个/init，访问它时将会向数据库中插入一些数据。代码如下：

> var express = require("express");     
> var mongoose = require('mongoose');
> 
> var models = require('./models');
> 
> var User = models.User;
> 
> mongoose.connect('mongodb://localhost/express-mongoose-demo');
> 
> var app = express.createServer();
> 
> // init data. Use "get" to simplify      
> app.get('/init', function(req, res) {      
>     var user = new User({      
>         email : 'nowind_lee@qq.com',      
>         name : 'Freewind'      
>     });      
>     user.save();      
>     res.send('Data inited');      
> });
> 
> app.listen(3000);      
> 
>  

首先导入了mongoose模块，以前之前定义的models.js。然后将models.User取出来供下面使用。

下面这句话用于连接mongodb，这里使用express-mongoose-demo

> mongoose.connect('mongodb://localhost/express-mongoose-demo');

接着定义/init，为了演示方便起见，这里使用http get，可直接在浏览器中访问该url。如果在真实程序中，应该使用app.post('/init', ...)

打开浏览器，访问：[http://localhost:3000/init](http://localhost:3000/init)

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb22.png "image")](http://freewind.me/wp-content/uploads/2012/05/image22.png)

提示数据已经初始化。使用mongodb的控制台，查询结果如下：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb23.png "image")](http://freewind.me/wp-content/uploads/2012/05/image23.png)

看到数据的确已经插入到mongodb中了。

## 五、/users

接着实现/users，查询User数据，并以json格式返回到浏览器端。代码很简单，如下：

> app.get('/users', function(req, res) {     
>     User.find(function(err, doc) {      
>         res.json(doc);      
>     });      
> });
> 
>  

打开浏览器，访问[http://localhost:3000/users](http://localhost:3000/users)

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb24.png "image")](http://freewind.me/wp-content/uploads/2012/05/image24.png)

果然看到json数据过来了。

但看看这段代码，里面有两个嵌套。如果数据再复杂一些的话，可能嵌套更多，就不易读了。能不能想办法让它简化一点呢？

下面就该express-mongoose出场了。

## 六、express-mongoose

express-mongoose项目就是为了简化express和mongoose。首先导入它：

> require('express-mongoose');

然后将/users方法改写成：

> app.get('/users', function(req, res) {     
>     res.send(User.find());      
> });
> 
>  

看这里，直接用res.send就直接发送了，不用传回调函数了，代码简单了很多。

重启app.js，再访问：[http://localhost:3000/users](http://localhost:3000/users)，截图如下：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb25.png "image")](http://freewind.me/wp-content/uploads/2012/05/image25.png)

果然效果一样。

如果去掉express-mongoose，会是什么效果呢？让我们先去掉：

> require('express-mongoose');

再访问：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb26.png "image")](http://freewind.me/wp-content/uploads/2012/05/image26.png)

现在返回的就是User.find()这个query的值，而不是查询结果。

今天就到这里，以后将慢慢加入更多的功能。