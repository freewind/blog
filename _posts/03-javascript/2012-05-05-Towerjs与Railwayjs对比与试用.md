---
layout: post
id: 878
alias: towerjs-railwayjs-comparasion
tags: JavaScript
date: 2012-05-05 23:56:02
title: Towerjs与Railwayjs对比与试用
---

在js后端框架中，towerjs与railwayjs是两个仿ror的全栈框架。两者在github上的关注度很相似，都在800左右，不相上下。

关于功能的对比，在stackoverflow上发现了一个汇总：[http://stackoverflow.com/questions/9897017/railwayjs-vs-towerjs](http://stackoverflow.com/questions/9897017/railwayjs-vs-towerjs) 

<table border="1">
<tbody>
<tr>
<th> </th>
<th>RailwayJS</th>
<th>Tower.js</th>
</tr>
<tr>
<td>First commit</td>
<td>Jan 2011</td>
<td>Oct 2011</td>
</tr>
<tr>
<td>Rails</td>
<td>2.3.x</td>
<td>3.x</td>
</tr>
<tr>
<td>Node.js</td>
<td>>= 0.4.x</td>
<td>>= 0.4.x</td>
</tr>
<tr>
<td>Server</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>Client</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Template agnostic</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>Default engine</td>
<td>EJS</td>
<td>CoffeeKup</td>
</tr>
<tr>
<td>Database agnostic</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>Default datastore</td>
<td>MongoDB</td>
<td>MongoDB</td>
</tr>
<tr>
<td>Model validations</td>
<td>validatesPresenceOf('email')</td>
<td>validates('email', presence: true)</td>
</tr>
<tr>
<td>Query scopes</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>Chainable scopes</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Param parsing</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Controllers</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>Resource controllers</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>File naming</td>
<td>users_controller.js</td>
<td>usersController.coffee</td>
</tr>
<tr>
<td>vm.runInCustomContext</td>
<td>*</td>
<td> </td>
</tr>
<tr>
<td>Asset pipeline</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Asset compression</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Routing</td>
<td>map.resources('posts')</td>
<td>@resources 'posts'</td>
</tr>
<tr>
<td>Nested routes</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>Generated url helpers</td>
<td>*</td>
<td> </td>
</tr>
<tr>
<td>Generators</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>Command-line api</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>REPL (console)</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>CoffeeScript console</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Asset cache method</td>
<td>timestamp</td>
<td>md5 hash</td>
</tr>
<tr>
<td>Production asset path</td>
<td>/app.css*123123123</td>
<td>/app-859c828c89288hc8918741.css</td>
</tr>
<tr>
<td>Preferred Language</td>
<td>JavaScript</td>
<td>CoffeeScript</td>
</tr>
<tr>
<td>CoffeeScript support</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>Internationalization</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>Heroku support</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>String case</td>
<td>snake_case</td>
<td>camelCase</td>
</tr>
<tr>
<td>Form builder</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>Semantic form builder</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Table builer</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>File watcher API</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Live-reload assets</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Test suite</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Generators for tests</td>
<td> </td>
<td>*</td>
</tr>
<tr>
<td>Twitter Bootstrap</td>
<td>*</td>
<td>*</td>
</tr>
<tr>
<td>HTML5 Boilerplate</td>
<td> </td>
<td>*</td>
</tr>
</tbody>
</table>

从上表可以看出，两者之间的主要差别在于风格。例如railwayjs仿的是ror2.x，而towerjs仿的是ror3.x。变量名风格、习惯使用纯js还是coffee等，都将会是人们选择时的一个考量。这里分别对两者进行一个简单的试用，取得第一感受。

## Railwayjs

官网：[http://railwayjs.com/](http://railwayjs.com/)

官网首页截图，典型的Bootstrap风格：

[![image](http://freewind.me/wp-content/uploads/2012/05/image7.png "image")](http://freewind.me/wp-content/uploads/2012/05/image7.png) 

Railswayjs的试用非常顺利，在win7 x64下也能顺利安装。

<div class="mycode">
<div class="mycode">

    sudo npm install railway -g
    </p></div>
    </div>

    生成一个blog项目：

    railway init blog && cd blog
        npm install -l
        railway generate crud post title content
        railway server 8888
        open [http://127.0.0.1:8888/posts](http://127.0.0.1:8888/posts)

    在开发模式下，它默认使用了redis这个内存数据库，不用安装，直接启动，异常方便。

    打开浏览器，访问[http://127.0.0.1:8888/posts](http://127.0.0.1:8888/posts)，页面如下：

    [![image](http://freewind.me/wp-content/uploads/2012/05/image8.png "image")](http://freewind.me/wp-content/uploads/2012/05/image8.png) 

    创建一个Post:

    [![image](http://freewind.me/wp-content/uploads/2012/05/image9.png "image")](http://freewind.me/wp-content/uploads/2012/05/image9.png) 

     

    创建后，回到列表页：

    [![image](http://freewind.me/wp-content/uploads/2012/05/image10.png "image")](http://freewind.me/wp-content/uploads/2012/05/image10.png) 

    查看其中一个：

    [![image](http://freewind.me/wp-content/uploads/2012/05/image11.png "image")](http://freewind.me/wp-content/uploads/2012/05/image11.png) 

    删除：

    [![image](http://freewind.me/wp-content/uploads/2012/05/image12.png "image")](http://freewind.me/wp-content/uploads/2012/05/image12.png) 

    在访问过程中，控制台会显示出请求信息：

    [![image](http://freewind.me/wp-content/uploads/2012/05/image13.png "image")](http://freewind.me/wp-content/uploads/2012/05/image13.png) 

    总之，页面精美，操作流畅，考虑周到，赞一个。

    具体的代码不贴了，请直接上官网看。

    ## TowerJs

    官网：[http://towerjs.org/](http://towerjs.org/)

    首页截图，看起来也比较素雅：

    [![image](http://freewind.me/wp-content/uploads/2012/05/image14.png "image")](http://freewind.me/wp-content/uploads/2012/05/image14.png) 

    Towerjs的试用过程比较坎坷。首先在windows下安装，有一些依赖的模块安装不上，可能还不支持windows。只好打开虚拟机，启动centos6.x，开始安装。

    <div class="mycode">
    npm install tower -g
    </div>

    然后生成一个试用网站：

    tower new app
    cd app
    tower generate scaffold Post title:string body:text belongsTo:user
    tower generate scaffold User email:string firstName:string lastName:string hasMany:posts

    也很顺利地生成了很多文件。

    启动：

    sudo chmod +x server.js
    sudo node server.js

    此时会提示一些第三方模块没有安装上，运行cake命令时，也会有类似提示。于是得分别安装：

    sudo npm install coffee-script

    sudo npm install forever

    需要注意的是，不能加-g，必须把模块们都装在当前目录下（生成node_modules），否则还会提示找不到模块。这一点困扰了我半天才解决。

    然后再次运行：

    node server.js

    会提示连不上数据库。原来towerjs默认使用了mongodb，我们必须先安装mongodb。

    到mongodb官网下载最新的mongodb源代码：[http://www.mongodb.org/downloads](http://www.mongodb.org/downloads)

    下载后解压，可以看到其中的bin目录，已经包含了可执行的各文件。

    sudo mkdir /data/db
    sudo chown `id -u` /data/db
    cd /mondodb-xxxx/bin
    ./mongod

    如果一切正常，会提示成功。再次启动towerjs生成的app，如果一切正常，会提示如下信息：

    [Sat, 05 May 2012 15:38:00 GMT] INFO Tower development server listening on port 3000

    在浏览器中访问：[http://localhost:3000](http://localhost:3000)，出现如下页面：

    [![image](http://freewind.me/wp-content/uploads/2012/05/image15.png "image")](http://freewind.me/wp-content/uploads/2012/05/image15.png) 

    什么情况？怎么这么丑？！

    打开firebug:

    [![image](http://freewind.me/wp-content/uploads/2012/05/image16.png "image")](http://freewind.me/wp-content/uploads/2012/05/image16.png)

    居然有这么多文件找不到？打开资源浏览器，发现对应的less文件存在：

    [![image](http://freewind.me/wp-content/uploads/2012/05/image17.png "image")](http://freewind.me/wp-content/uploads/2012/05/image17.png)

    看来需要手动把它们转为.css。

    按照官网首页提示，输入：

    cake assets:compile

提示：`No such task: assets:compile`

奇怪，看一下任务列表：

[![image](http://freewind.me/wp-content/uploads/2012/05/image18.png "image")](http://freewind.me/wp-content/uploads/2012/05/image18.png)

哪有编译任务？！实在进行不下去了。

试下添加User:

[![image](http://freewind.me/wp-content/uploads/2012/05/image19.png "image")](http://freewind.me/wp-content/uploads/2012/05/image19.png) 

中文显示正常：

[![image](http://freewind.me/wp-content/uploads/2012/05/image20.png "image")](http://freewind.me/wp-content/uploads/2012/05/image20.png) 

试用到此结束，不能不说有些失望。安装过程复杂，也不能提供开箱即用的数据库，文档与实际命令有差异，无法编译资源文件，导致最终页面相当丑。

与railwayjs的试用体验相差甚远。railwayjs一共只花了十分钟，而towerjs花了一个多小时。

所以在这两者之间，我将选择基于Railwayjs开发。
