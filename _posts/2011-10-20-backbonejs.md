---
layout: post
title: backbone.js
tags:
  - JavaScript
date: 2011-10-20 18:17:17
---

![](http://freewind.me/wp-content/uploads/2011/10/zrclip_001p20091490.png)

backbone.js是一个javascript库。当我们需要在页面中写大量的javascript代码来处理页面逻辑时，backbone非常有用，因为它的设计目的就是把javascript代码分解为不同的层来让代码更加清晰。

网址：[http://documentcloud.github.com/backbone/](http://documentcloud.github.com/backbone/)

名字backbone中文是"脊椎"，可以想到它的定位：就像脊椎骨一样撑起整个程序的结构，其它的javascript代码，都是附着在它上面的血肉。

在backbone里，有这样几个概念：Model, View, Router, Collection, Events。没错，它有MVC结构。

Model就像是javabean，它对应的是数据。View对应的是页面上的某一个组件，可以增加各种改变页面效果的方法。Router可用来将页面上的链接（准确的说，是用不同的锚点来代替可跳转的链接）与某些方法联系起来。Collection是一些Model的集全，它比Model多一些事件。Event用来表明发生了什么操作。

</p>

<span id="more-446"></span>

刘松利用backbone.js和coffeescript写了一个简单的程序，是一个简单的聊天室程序的页面。我先把它们的代码贴在这里。

**main.html**

> <!DOCTYPE html>     
> <html>      
> <head>      
>     <title></title>      
>     <style>      
>             /** reset **/      
>         body {      
>             font-size: 14px;      
>         } 
> 
>         body, ul, li, div, h3 {     
>             padding: 0;      
>             margin: 0;      
>         } 
> 
>         ul {     
>             list-style: none;      
>         } 
> 
>             /** layout **/     
>         .main {      
>             display: box;      
>             box-orient: horizonal; 
> 
>             display: -moz-box;     
>             -moz-box-orient: horizonal;      
>             display: -webkit-box;      
>             -webkit-box-orient: horizonal; 
> 
>             width: 100%;     
>         } 
> 
>         .content {     
>             box-flex: 1; 
> 
>             -moz-box-flex: 1;     
>             -webkit-box-flex: 1;      
>         } 
> 
>             /** header **/     
>         .header {      
>             background-image: linear-gradient(top, #6381B8, #264175);      
>             background-image: -moz-linear-gradient(top, #6381B8, #264175);      
>             background-image: -webkit-linear-gradient(top, #6381B8, #264175); 
> 
>             box-shadow: 0 1px 0 #809DD4 inset, 0 -1px 0 #000000 inset;     
>             height: 40px;      
>         } 
> 
>             /** sidebar **/     
>         .sidebar {      
>             height: 600px;      
>             width: 200px;      
>             background-color: #DFE6EE;      
>             border-right: 1px solid #B4BBC4;      
>         } 
> 
>         .sidebar h3 {     
>             background-image: linear-gradient(top, #EDF3FA, #D0D9E4);      
>             background-image: -moz-linear-gradient(center top, #EDF3FA, #D0D9E4);      
>             background-image: -webkit-linear-gradient(top, #EDF3FA, #D0D9E4); 
> 
>             border-top: 1px solid #FFFFFF;     
>             border-bottom: 1px solid #B4BBC4;      
>             color: #747D84;      
>             text-shadow: 0 1px 0 #FFFFFF;      
>             font-size: 14px;      
>             line-height: 20px;      
>             font-weight: bold;      
>             text-indent: 10px;      
>         } 
> 
>         .sidebar ul li a {     
>             box-shadow: 0 1px 0 #EEF6FF inset, 0 -1px 0 #D3D9E1 inset;      
>             display: block;      
>             line-height: 26px;      
>             padding-left: 6px;      
>             text-decoration: none;      
>             color: #000;      
>             cursor: pointer;      
>             font-size: 12px;      
>             /*-webkit-transition:All 0.1s ease;*/      
>             /*-moz-transition:All 0.1s ease;*/      
>             border-left: 3px solid transparent;;      
>         } 
> 
>         .sidebar ul li a:hover {     
>             border-left: 3px solid #748DBB;      
>         } 
> 
>         .sidebar ul li.active a {     
>             background-color: #748DBB;      
>             background-image: linear-gradient(center top, #ADBDD7 0pt, #748DBB 100%);      
>             background-image: -moz-linear-gradient(center top, #ADBDD7 0pt, #748DBB 100%);      
>             background-image: -webkit-linear-gradient(top, #ADBDD7 0pt, #748DBB 100%);      
>             box-shadow: 0 1px 0 #899CC0 inset, 0 2px 0 #B4C6E4 inset, 0 -1px 0 #6C7B98 inset;      
>             margin: -1px 0 0;      
>             text-shadow: 0 1px 1px #474E59;      
>             font-weight: bold;      
>             display: block;      
>             line-height: 30px;      
>             color: #FFFFFF;      
>         } 
> 
>             /** content **/     
>         .content {      
>             color: #444444;      
>         } 
> 
>         .content > h3 {     
>             background: linear-gradient(center top, #F3F3F3, #D7D7D7) repeat scroll 0 0 #E7E7E7;      
>             background: -moz-linear-gradient(center top, #F3F3F3, #D7D7D7) repeat scroll 0 0 #E7E7E7;      
>             background: -webkit-linear-gradient(top, #F3F3F3, #D7D7D7) repeat scroll 0 0 #E7E7E7;      
>             border-bottom: 1px solid #B7B7B7;      
>             border-top: 1px solid #FFFFFF;      
>             color: #777777;      
>             font-size: 14px;      
>             font-weight: bold;      
>             line-height: 20px;      
>             text-shadow: 0 1px 0 #FFFFFF;      
>             padding-left: 10px;      
>         } 
> 
>         .chats li.chat {     
>             border-top: 1px solid #ECECEC;      
>             line-height: 26px;      
>             text-indent: 10px;      
>         } 
> 
>             /** activeChatView **/     
>         .chats li.chat.active {      
>             background-image: linear-gradient(center top, #ADBDD7 0pt, #748DBB 100%);      
>             background-image: -moz-linear-gradient(center top, #ADBDD7 0pt, #748DBB 100%);      
>             background-image: -webkit-linear-gradient(top, #ADBDD7 0pt, #748DBB 100%);      
>             box-shadow: 0 1px 0 #899CC0 inset, 0 2px 0 #B4C6E4 inset, 0 -1px 0 #6C7B98 inset;      
>             margin: -1px 0 0;      
>             text-shadow: 0 1px 1px #474E59;      
>             color: #FFF;      
>         } 
> 
>             /** writer **/     
>         .writer {      
>             position: fixed;      
>             bottom: 0;      
>             height: 100px;      
>         } 
> 
>     </style>     
>     <script src="jquery.js"></script>      
>     <script src="socket.io.js"></script>      
>     <script src="underscore.js"></script>      
>     <script src="backbone.js"></script>      
>     <script src="app.js"></script>      
> </head>      
> <body>      
> <div class="header"> 
> 
> </div>     
> <div class="main"> 
> 
>     <div class="sidebar">     
>         <h3>标题</h3>      
>         <ul>      
>             <li><a href="#">好啊</a></li>      
>             <li><a href="#">好啊</a></li>      
>             <li><a href="#">好啊</a></li>      
>             <li><a href="#">好啊</a></li>      
>             <li class="active"><a href="#">好啊</a></li>      
>             <li><a href="#">好啊</a></li>      
>         </ul>      
>     </div>      
>     <div class="content">      
>         <h3>这是标题 &amp;gt; 好啊</h3>      
>         <ul class="chats">      
>             <li class="chat">这是一条</li>      
>             <li class="chat">这是一条</li>      
>             <li class="chat">这是一条</li>      
>             <li class="chat">这是一条</li>      
>             <li class="chat">这是一条</li>      
>             <li class="chat">这是一条</li>      
>             <li class="chat">这是一条</li>      
>             <li class="chat">这是一条</li>      
>         </ul>      
>         <div class="writer">      
>             <textarea class="writerInput"></textarea>      
>             <button class="sendBtn">发送</button>      
>         </div>      
>     </div>      
> </div>      
> </body>      
> </html>

从中可以看到，html页面中的代码十分干净，只有html和css定义，没有复杂的js。

**app.coffee**

> root = this     
> root.App = App =      
>     Models: {}      
>     Views: {}      
>     Collections: {}      
>     chatsView: null      
>     topicView: null      
>     writerView: null      
>     io: null      
>     initialize: -> 
> 
> class App.Models.Chat extends Backbone.Model     
>     defaults:      
>         id: null      
>         content: ""      
>         author: null      
>         timestamp: null 
> 
> class App.Models.Topic extends Backbone.Model     
>     defaults:      
>         id: null      
>         title: ""      
>         description: "" 
> 
> class App.Collections.Chats extends Backbone.Collection     
>     initialize: ->      
>         super      
>         @io = App.io      
>         @io.on("message.send", @on_message_send)      
>     on_message_send: (msg) =>      
>         chat = new App.Models.Chat(msg)      
>         @add(chat) 
> 
> class App.Collections.Topics extends Backbone.Collection 
> 
> class App.Views.ChatsView extends Backbone.View     
>         initialize: ->      
>             @collection.bind "add", @addChat      
>             @activeChatView = null      
>         render: ->      
>             @collection.each @addChat      
>             @      
>         addChat: (model) =>      
>             chatView = new App.Views.ChatView(model)      
>             $(@el).append(chatView.el)      
>         markActive: (chatView) ->      
>             $(@activeChatView.el).removeClass("active") if @activeChatView?      
>             @activeChatView = chatView      
>             $(@activeChatView.el).addClass "active" 
> 
> class App.Views.ChatView extends Backbone.View     
>     TEMPLATE : """ <span class="content"><%= content%></span>      
>            <span class="timestamp"><%= timestamp%></span>      
>            <span class="author"><%= author%></span> 
> 
>            """     
>     tagName: "li"      
>     className: "chat"      
>     initialize: (@model) ->      
>         model.bind("change", @render)      
>         @render()      
>     render: ->      
>         $(@el).html @model.get("content")      
>         this      
>     events:      
>         click : "markActive"      
>     markActive: ->      
>         App.chatsView.markActive(@) 
> 
> class App.Views.WriterView extends Backbone.View     
>     events:      
>         "click .sendBtn" : "send_message"      
>         "keyup .writerInput" : "send_message_on_enter"      
>     send_message: ->      
>         input = @$(".writerInput")      
>         return unless (value = input.val())?      
>         App.io.emit("message.send", content: value)      
>         input.val("")      
>     send_message_on_enter: (event)->      
>         @send_message() if event.keyCode == 13 
> 
> App.initialize = ->     
>     App.io = root.io.connect("[http://localhost:8081")](http://localhost:8081"))      
>     chat = new App.Models.Chat(content: "这是个试验1")      
>     chats = new App.Collections.Chats([chat])      
>     App.chatsView = new App.Views.ChatsView(collection: chats, el: $(".chats")).render()      
>     App.writerView = new App.Views.WriterView(el: $(".writer")).render() 
> 
> $ ->     
>     App.initialize()

这些coffee代码，与js代码比起来可读性更好一些。当然，它们最终将被转换为js链到页面中。

下面的这些对话，就是我向刘松请教backbone的过程，仔细看一下，基本上就能明白backbone.js是怎么回事了。

> 我(23246779) 11:49:03      
> 我有backbone方面的问题想问你
> 
> 刘松(42279444) 12:12:11      
> 我到了。
> 
> 我(23246779) 12:12:59      
> backbone到底是用来做什么的       
> 我虽然有个大概的感觉，但还是不太清楚
> 
> 刘松(42279444) 12:14:08      
> 参考这里的代码：       
> [https://github.com/scalaeye/scalaeye/blob/sliu/html/app.coffee](https://github.com/scalaeye/scalaeye/blob/sliu/html/app.coffee)       
> 在js端也使用mvc框架，分离数据、UI的职责
> 
> 我(23246779) 12:14:45      
> 我看到你的main.html，里面很干净       
> 只有html和css
> 
> ![](http://freewind.me/wp-content/uploads/2011/10/zrclip_002p6ade9a67.png)
> 
> 刘松(42279444) 12:14:37      
> 它也提供了事件抽象。       
> 比方咱们的页面，应该有TopicView(主题界面）、ChatsView(含多个ChatView)和WriterView(输入界面）
> 
> 刘松(42279444) 12:15:42      
> 这是ui层。       
> 对应backbone就是views
> 
> 我(23246779) 12:16:16      
> 这几个view在html代码中，有没有表现出来？
> 
> 刘松(42279444) 12:16:53      
> 对.
> 
> 刘松(42279444) 12:17:13      
> <ul class="chats" 对应的ChatsView       
> writer对应WriterView       
> TopicView我还没来得及玩，应该对应sidebar里的某个区
> 
> 我(23246779) 12:19:10      
> 哦，需要事先在html中定义好块       
> 每个"块"可看作一个view的占位符
> 
> 刘松(42279444) 12:19:28      
> 对。       
> 其实这些东西 backbone没有强制要求
> 
> 我(23246779) 12:20:19      
> 在app.coffee中，定义好几个view       
> 每个view都是继承于Backbone.View
> 
> 刘松(42279444) 12:20:29      
> 你可以在backbone的render方法里定义它的内容，或者在自己的js里全部生成所有html。或者全部使用html,只用backbone绑定，都行。       
> 一个View对应一个html区块，具体这个区块是用js生成还是原来html写好的，还是一部分生成，backbone都支持。
> 
> 我(23246779) 12:21:17      
> 这些view，感觉就像是一个个组件？
> 
> 刘松(42279444) 12:21:35      
> 是的，一个个组件
> 
> 我(23246779) 12:21:17      
> 里面还定义了一些方法，比如render, addChat, markActive
> 
> 刘松(42279444) 12:21:51      
> addChat、markActive是我自定义方法
> 
> 我(23246779) 12:22:18      
> view中定义的这些方法，是不是都是对页面的修改？
> 
> 刘松(42279444) 12:22:30      
> render在它里面有特殊含义，当这个view绑定的model对象发生改变时，render会被自动调用，相应于重绘       
> 定义这些方法主要是看怎么给这个组件定义行为，可以是读取ui信息，修改ui，修改model，或其它的，没有限制。
> 
> 我(23246779) 12:22:57      
> 这个方便
> 
> 我(23246779) 12:23:26      
> 还定义了一些Model，它们可看作是javabean?       
> model中并没有定义额外的方法
> 
> 刘松(42279444) 12:24:03      
> 对，model相当于javabean
> 
> 我(23246779) 12:24:45      
> model与view之间的定义关系，是任意的吗？
> 
> 刘松(42279444) 12:25:17      
> 常规的一个view会对应一个model对象，或一个collection对象。       
> 从实现上也不是强制的。
> 
> 我(23246779) 12:25:53      
> view怎么跟model绑定？
> 
> 刘松(42279444) 12:25:59      
> new XXView(model: xxx)
> 
> 刘松(42279444) 12:26:10      
> 看app.coffee的最后几行。       
> 91行。       
> 我传给它一个collection       
> 一个collection就是一个数组，它比数组多了：       
> 当往这个数组里增加、删除元素时，会trigger "add" "remove"事件
> 
> 我(23246779) 12:27:31      
> Chat这个model有没有跟哪个view绑定？
> 
> 刘松(42279444) 12:30:32      
> 哦，看89行，我new 出一个Chat对象，放到 collection里，再由collection创建了ChatsView。在ChatsViews的initialize里（41行）循环collection构建的各个ChatView       
> 相当于ChatsView是ChatView的manager，所有ChatView（ui)的构建、删除，都在ChatsView里。       
> 恶，说错了，我是在ChatsView的render里画的。
> 
> 我(23246779) 12:31:43      
> 原来如此，圈还转得挺大的
> 
> 我(23246779) 12:32:02      
> 为什么有model，还有collection？       
> 它们两个是什么关系？       
> 用法应该是一样的吧？只是collection多了一些不同的event
> 
> 刘松(42279444) 12:32:32      
> 是的。       
> 我理解collection是一种特殊的model: 代表数组，有不同的event
> 
> 我(23246779) 12:33:11      
> root = this       
> 这个是什么意思？
> 
> 刘松(42279444) 12:33:30      
> 你看它编译完的js文件：       
> [https://github.com/scalaeye/scalaeye/blob/sliu/html/app.js](https://github.com/scalaeye/scalaeye/blob/sliu/html/app.js)       
> 前后用function(){}包起来了，把this当参数传了进去。       
> 在网页上this是window对象。
> 
> 我(23246779) 12:34:51      
> 哦，这样的话，js中调用this时，都会指向这个window对象
> 
> 刘松(42279444) 12:34:49      
> 是的。       
> 它这样做是为了避免向命名空间引入过多的垃圾       
> 如果要引入全局变量，必须显式的用root.xx = xx定义。
> 
> 我(23246779) 12:36:00      
> coffee中以@开头的，表示什么？
> 
> 刘松(42279444) 12:35:54      
> this       
> @a 等价于 this.a
> 
> 我(23246779) 12:36:24      
> 哦，好
> 
> 刘松(42279444) 12:36:37      
> 看78行。       
> 猜猜干什么的
> 
> 我(23246779) 12:37:48      
> 得到html上<div class="writerInput" />这个块？
> 
> 刘松(42279444) 12:37:42      
> 是的。       
> @$("...")       
> 等价于this.$("...")
> 
> 我(23246779) 12:38:10      
> 这里的$，等价于jquery中的$?       
> 能否直接用$(".writerInput")，前面不加@?
> 
> 刘松(42279444) 12:38:15      
> 不完全等于
> 
> 刘松(42279444) 12:38:35      
> Backbone.View提供了一个$方法。       
> 跟jquery的全局$方法用法完全一样。       
> 区别是       
> view.$(".a") 相当于       
> $(".a", view.el)
> 
> 我(23246779) 12:39:55      
> .el是什么意思？
> 
> 刘松(42279444) 12:39:54      
> 在view对应的区块的根元素（el）范围内查".a"       
> 这个功能我很喜欢。
> 
> 我(23246779) 12:40:43      
> 能否同时使用jquery的$?
> 
> 刘松(42279444) 12:40:36      
> 可以。
> 
> 我(23246779) 12:41:06      
> 通常用backbone的就够了？
> 
> 刘松(42279444) 12:41:00      
> 实际上，backbone的view.$的实现，就是调了全局的$方法       
> 所以需要依赖jquery或zepto
> 
> 我(23246779) 12:41:26      
> 哦，所以还是得加上jquery才行？
> 
> 刘松(42279444) 12:41:18      
> 似乎是。       
> 如果不用这个this.$，就不需要引入。       
> 我没查证哈       
> 原理是这样的。
> 
> 我(23246779) 12:42:21      
> 好
> 
> 刘松(42279444) 12:42:17      
> 这个很实用。
> 
> 我(23246779) 12:42:32      
> 还有两个问题       
> 在backbone中，看到有一些save这样的方法       
> 是把model保存在服务器上的，这怎么实现的       
> 怎么用？
> 
> 刘松(42279444) 12:43:51      
> 它有个叫Backbone.sync的方法，当调用model.save, model.destroy, collection.fetch等方法时，会根据一定规则拼一个url出来，通过jquery或zepto的ajax支持发请求。       
> 如果用websocket做backend的话，需要改写Backbone.sync，这样才能支持当model.save时，向websocket发数据。
> 
> 我(23246779) 12:44:40      
> 在服务器端收到请求后，解析url，再生成一个对应的model，保存之
> 
> 刘松(42279444) 12:44:37      
> 对。       
> 如果不使用它的save, fetch方法，就不会调Backbone.sync。       
> 也没关系。
> 
> 我(23246779) 12:45:20      
> 有没有哪个库，在服务器端与backbone是对应的？       
> 不需要手动解析       
> 如同socket.io的服务器端与客户端的关系
> 
> 刘松(42279444) 12:45:29      
> backbone似乎有服务器端版本。
> 
> 刘松(42279444) 12:45:38      
> 我不明白你说的手工解析。
> 
> 我(23246779) 12:46:16      
> 我的意思是，假如我用play，还需要先查它拼url的规则       
> 再按这个规则来取对应的数据
> 
> 刘松(42279444) 12:46:08      
> rails的Restful支持不错，backbone跟rails结合比较好。       
> play也是完整的Restful支持，你只需要按它的规则去配routes
> 
> 我(23246779) 12:46:54      
> backbone还有一个地方       
> Router       
> var Workspace = Backbone.Router.extend({
> 
> routes: {      
> "help": "help", // #help       
> "search/:query": "search", // #search/kiwis       
> "search/:query/p:page": "search" // #search/kiwis/p7       
> },
> 
> help: function() {      
> ...       
> },
> 
> search: function(query, page) {      
> ...       
> }
> 
> });      
> 看这个例子，routes里，key是一个个url?
> 
> 刘松(42279444) 12:47:34      
> 我们这个应用里，没必要用Router，至少目前没发现这个必要。
> 
> 我(23246779) 12:47:59      
> 嗯。我是看文档时，这一点不太明白
> 
> 刘松(42279444) 12:49:30      
> 我理解，就是对于单个页面，一直不刷新的应用，页面上有好多链接、按钮，点击都会执行不同的操作，比如与服务器端交互、更新本地ui片段，就是不刷新。       
> 这样，将这些链接的地址用router的方式声明起来，就有了规划。       
> 它还支持一个pushState的选项，我没研究，似乎是可以支持 浏览器历史的前进、后退。
> 
> 我(23246779) 12:50:24      
> 有没有实际使用过该功能？
> 
> 刘松(42279444) 12:50:18      
> 没有。
> 
> 我(23246779) 12:50:38      
> 我就是有点奇怪，点了一个链接后，页面都跳转了       
> 岂不是白定义了
> 
> 刘松(42279444) 12:50:31      
> 没有。       
> 它是用anchor       
> 链接后是 "#xxx"       
> 锚点       
> 不刷新。
> 
> 我(23246779) 12:51:26      
> 哦，你的意思是，页面中不出现可跳转的url?       
> 而是定义一些锚点，把它们看作是url?
> 
> 刘松(42279444) 12:51:20      
> 嗯。       
> 对。       
> 锚点里也可以携带一些有意义的信息，如业务类型、id，把它个形式设计的跟RESTful api的url规则很象。       
> 但锚点不会引发页面跳转。       
> 就象github里看代码时，点左侧的页号一样。
> 
> 我(23246779) 12:53:38      
> 这一点很有趣，也很精彩
> 
> 刘松(42279444) 12:54:06      
> 是啊，这样既起到规划作用，又能保存历史记录，当点击浏览器的前进、后退也好使（跟gmail那样）
> 
> 我(23246779) 12:54:50      
> 通过锚点，还能让浏览器的前进后退也能用？       
> 那太好了，我一直没想明白，怎么跨浏览器做到这一点呢
> 
> 刘松(42279444) 12:56:23      
> 点击锚点不会页面刷新，浏览器却能存到history列表里，当切换锚点时，js能收到location change的event，所以能做这件事。       
> 但history的保存、恢复也需要js做很多事，不知道backbone能不能达到这个效果，不过我觉得这不重要，整体感觉router在我们这个案例里没有用处。       
> 我甚至觉得model.save等也没必要用，也就不用改写它的sync方法了。
> 
> 我(23246779) 12:53:38      
> 还有一点       
> 每个model都定义了一些默认的event的       
> 是不是所有的event，都会触发对应的view的render方法？
> 
> 刘松(42279444) 12:54:58      
> 我原先以为是，后来试验着发现不是。       
> 所以我在view的initialize方法（构造方法）里显式的绑定了。       
> 象63行。
> 
> 我(23246779) 12:55:45      
> 嗯，看到了       
> 这样子代码之间隔离得很清楚       
> 只要明白了backbone的规则，就能理解它的运转方式
> 
> 我(23246779) 12:57:15      
> 也不像之前用jquery那样，给某个div绑函数时，写得乱七八糟       
> backbone+coffeescript，太好了       
> 让人对javascript编程完全有了新的感觉
> 
> 刘松(42279444) 12:58:38      
> 哈哈，是啊。
> 
> 我(23246779) 12:59:12      
> 这个功能：写上内容，点发送，提交给服务器，怎么实现
> 
> 刘松(42279444) 12:58:58      
> 看74行。
> 
> 我(23246779) 13:00:05      
> 这个把button的click事件，绑定到一个方法上了
> 
> 刘松(42279444) 13:00:30      
> 对。       
> 一个组件上的所有事件在一个地方声明，把对应的行为（方法）也对应起来了。       
> 而且       
> 声明方式是：       
> "event selector" : "方法名"       
> 这个selector，不是在body下去找，而是在当前view的根结点上去找，效率好，不易出错。
> 
> 我(23246779) 13:02:25      
> 嗯，这样好
> 
> 刘松(42279444) 13:02:25      
> 我没看backbone的代码，还有可能是用jquery的live，使用event delegation做 的。
> 
> 我(23246779) 13:01:07      
> 我明白那个Router的作用了，它就是在backbone中用来代替以前的超链接       
> 我们平时开发网站，会用到大量的超链接（会跳转）       
> 改用backbone后，我们可以利用锚点沿用这个习惯
> 
> 刘松(42279444) 13:01:16      
> 嗯
> 
> 我(23246779) 13:02:46      
> 还有一个小问题
> 
> 我(23246779) 13:02:53      
> 你的代码里有这样的：aaa if bbb?       
> 最后一个问号是什么意思？       
> 为什么不写成aaa if bbb
> 
> 刘松(42279444) 13:03:09      
> 哦。       
> if bbb.isDefined()       
> 我追时髦了。
> 
> 我(23246779) 13:03:38      
> 哦，表示"存在"       
> 没什么其它的问题了       
> 呵呵，明白了       
> 真多谢你了，一席话让我基本上明白backbone是怎么回事了