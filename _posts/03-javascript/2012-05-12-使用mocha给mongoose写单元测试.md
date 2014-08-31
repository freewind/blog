---
layout: post
alias: use-mocha-to-write-unit-tests-for-mongoose
tags: JavaScript
date: 2012-05-12 16:21:54
title: 使用mocha给mongoose写单元测试
---

使用javascript写程序，我认为第一要务就是单元测试。所以当我把mongoose的简单例子跑通后，第一件事就是学着给它写单元测试。

## Jasmine

首先尝试的是Jasmine：[http://pivotal.github.com/jasmine/](http://pivotal.github.com/jasmine/)，冲着它3k+的关注值去的。写简单的例子很容易，但涉及到mongoose时，遇到了两个麻烦的问题：

1.  在beforeEach中调用mongoose的一些异步方法（如connect, drop, insert等）相当不方便2.  没有beforeAll

第1处不方便，是因为Jasmine提供了runs(), waits(), waitsFor()这三个方法来支持异步调用。其中在runs中调用异步方法，在waits中等待一定时间，或用waitsFor来检查一些条件来判断之前的异步方法是否完成。这种方式不方便之处就在于waitsFor中有时候不那么检查，所以通常要设一个局部变量来保存状态。如：

> <font style="background-color: #ffffff">it('should dosomthing', function() {</font>
> 
> <font style="background-color: #ffffff">    var ok = false;</font>
> 
> <font style="background-color: #ffffff">    doSomething(function(){</font>
> 
> <font style="background-color: #ffffff">        ok = true;</font>
> 
> <font style="background-color: #ffffff">    });</font>
> 
> <font style="background-color: #ffffff">    waitsFor( function() {</font>
> 
> <font style="background-color: #ffffff">        return ok; </font>
> 
> <font style="background-color: #ffffff">    }, 'Do something', 2000};</font>
> 
> <font style="background-color: #ffffff">    // 继续执行下面的代码</font>
> 
> <font style="background-color: #ffffff">});</font>

可以看到代码中定义的ok感觉有点多余，代码有点繁琐。

这几天为了找到一种看起来更简单一点的办法，我看了很多与同步异步相关的模块：[https://github.com/joyent/node/wiki/modules#wiki-async-flow](https://github.com/joyent/node/wiki/modules#wiki-async-flow)。虽然最终一个也没有用上，不过也算学习了基础知识，额外收获。（另外此处推荐由群友木木勇创建的一个Promise模块，简洁明了，代码不多，值得参考：[https://github.com/sail-sail/Promise](https://github.com/sail-sail/Promise "https://github.com/sail-sail/Promise")）

第2处是因为我想在所有测试前连接mongodb，所有测试后关闭连接，中间就不关闭了。但是Jasmine居然没有提供，而且有人还说“不用beforeAll可以让你的测试代码写得更好”。在这里可以看到一些讨论：[https://github.com/pivotal/jasmine/pull/56](https://github.com/pivotal/jasmine/pull/56)。从中可以看到有些人对于Jasmine相当不满，纷纷转到另一个测试框架：mocha.

## Mocha

Mocha是由express的作者创建的，品质有保证：[http://visionmedia.github.com/mocha/](http://visionmedia.github.com/mocha/)

相比Jasmine，它有以下几个优点：

1.  异步方法的测试更容易（赞！）2.  支持before,after（即beforeAll/afterAll)3.  与nodejs结合更自然4.  测试报告更清晰明了（比如两个字符串不相等时，会分别用红色和绿色标出相异部分）5.  可使用多种风格的DSL，比如should/expect等

第1处，对于异步方法的测试，mocha提供了一个非常简单的方法：传入一个callback，只有它被调用，才会执行下一步。这里重写前面的例子：

> <font style="background-color: #ffffff">it('should dosomthing', function(done) {</font>
> 
> <font style="background-color: #ffffff">  doSomething(done</font><font style="background-color: #ffffff">);</font>
> 
>   // 下面的代码只有当done在doSomething中被异步调用之后才会运行
> 
> <font style="background-color: #ffffff">});</font>

一行代码就完成了？简单太多了吧。

其它各处可参考官方文档，这里就不多说了。

## Mongoose

mongoose这一块花了我很多时间，主要还是因为对于异步编程模块的不适应，老是搞不定那些异步调用。比如，我在initdb方法中，想做以下几件事：

1.  连接mongodb2.  删除数据库3.  插入多条记录

这些方法都是异步的，通过回调执行下一步。我不想写那么多的嵌套语句，希望能找到一种类似同步方法调用的方式来写代码，可惜到最后还是没有发现特别满意的。期间看到了很多方案，但都觉得对于代码可读性的改善不大，最终放弃，还是用了嵌套的方式。

Mongoose提供了两种访问mongodb的方式，一种是通过它封装后的Model，一种是得到connnection对象使用底层的native driver。我在测试过程中，测试的对象是各Model相关方法，但验证时用到了native driver。这里不上代码不好说，先跳过。

## Before/After/BeforeEach

我在项目中定义了多个Model，如User/Channel等，我想为每一个都准备单独的测试文件。如何让它们共享before/after/beforeEach方法呢？解决方法比较简单：定义一个单独的文件，把这些方法写在里面，然后在其它文件中require它。

我写在一个db_globle.js中：

> var helper = require('./helper');
> 
> before(function(done){      
>     helper.connect(function(){done();});       
> });
> 
> after(function(done) {      
>     helper.close(function(){done();});       
> });
> 
> beforeEach(function(done){      
>     helper.initdb(function(){done();});       
> });       
> 
>  

然后在其它文件，如user.js中调用它：

> require('should');      
> require('./db_global');       
> var mongoose = require('mongoose');       
> var helper = require('./helper');
> 
> var models = require('../models');      
> var User = models.User;
> 
> describe('Users', function(){
> 
>     var users = helper.getConnection().collection('users');
> 
>     it('can be created', function(done){      
>         var user = new User({       
>             email:'xxx@xxx.com',       
>             name: 'XXX',       
>             salt: '123&#8242;,       
>             password: '456&#8242;       
>         });       
>         user.save();
> 
>         users.find({email:'xxx@xxx.com'}, function(err, cursor){      
>             cursor.toArray(function(err,docs) {       
>                 docs.should.have.lengthOf(1);       
>                 var u = docs[0];       
>                 u._id.should.not.be.null;       
>                 u.email.should.equal('xxx@xxx.com');       
>                 u.name.should.equal('XXX');       
>                 u.salt.should.equal('123&#8242;);       
>                 u.password.should.equal('456&#8242;);       
>                 done();       
>             });       
>         });       
>     });       
> });
> 
>  

其中的helper是一个自定义的辅助文件，它定义了操作mongodb的一些方法，大体如下：

> var mongoose = require('mongoose');
> 
> exports.DB = 'mongo://localhost/test';
> 
> exports.connect = function(callback) {      
>     mongoose.connect(exports.DB, callback);       
> };
> 
> exports.close = function(callback) {      
>     mongoose.connection.close(callback);       
> };
> 
> exports.getConnection = function() {      
>     return mongoose.connection;       
> };
> 
> exports.initdb = function(callback) {      
>     var conn = mongoose.connection;
> 
>     // drop database      
>     conn.db.dropDatabase(function(err){       
>         if(err) {       
>             return callback(err);       
>         }
> 
>         console.log('Database droped.');
> 
>         // insert users      
>         conn.collection('users').insert([{       
>             email: 'nowind_lee@qq.com',       
>             name: 'Freewind',       
>             salt: '111',       
>             password: '123456'       
>         }], function(err, docs) {       
>             // insert others       
>         });       
>     });       
> };
> 
>  

经过几天的努力，终于成功的在mocha中成功地写model测试了：）虽然花了很多时间，但很值得。

## 相关问题

在此期间遇到了很多问题，非常感谢万能的stackoverflow和各位群友，特别是为了我的问题忙到半夜的木木勇，还有热心的sapjax。在这里把我提的相关问题汇集如下：

1.  [Global `before` and `beforeEach` for mocha?](http://stackoverflow.com/questions/10561598/global-before-and-beforeeach-for-mocha)2.  [Global `beforeEach` in jasmine?](http://stackoverflow.com/questions/10560716/global-beforeeach-in-jasmine)3.  [Why doesn't the 2rd function run, in this javascript's async example?](http://stackoverflow.com/questions/10554079/why-doesnt-the-2rd-function-run-in-this-javascripts-async-example)4.  [Simplest way to wait some asynchronous tasks complete, in Javascript?](http://stackoverflow.com/questions/10551499/simplest-way-to-wait-some-asynchronous-tasks-complete-in-javascript)5.  [How to use module `q` to refactoring mongoose code?](http://stackoverflow.com/questions/10545087/how-to-use-module-q-to-refactoring-mongoose-code)6.  [How to test a method in Jasmine if the code in `beforeEach` is asynchronous?](http://stackoverflow.com/questions/10527394/how-to-test-a-method-in-jasmine-if-the-code-in-beforeeach-is-asynchronous)7.  [How to let the inserting to be synchronized, in mongoose?](http://stackoverflow.com/questions/10520715/how-to-let-the-inserting-to-be-synchronized-in-mongoose)8.  [How to insert a doc into mongodb using mongoose and get the generated id?](http://stackoverflow.com/questions/10520501/how-to-insert-a-doc-into-mongodb-using-mongoose-and-get-the-generated-id)9.  [How to do raw mongodb operations in mongoose?](http://stackoverflow.com/questions/10519432/how-to-do-raw-mongodb-operations-in-mongoose)10.  [How to organize the code if I want to initialize database before each tests?](http://stackoverflow.com/questions/10518671/how-to-organize-the-code-if-i-want-to-initialize-database-before-each-tests)11.  [Can't drop a database in mongoose?](https://github.com/LearnBoost/mongoose/issues/907)12.  [我的问题](https://gist.github.com/2660993)，以及[木木勇的非嵌套方式初始化数据库代码](https://github.com/sail-sail/Promise/blob/master/demo/dbTest.js)
