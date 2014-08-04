title: Couchdb+angularjs安装与试用
tags:
  - JavaScript
date: 2012-05-02 01:40:10
---

对于我正在寻找的基于js的快速开发方案，我今天尝试了一下couchdb+angularjs。

Couchdb是这几天让我觉得很振奋的一个项目。它与mongodb类似，是一个基于文档的nosql数据库，但不同的是，mongodb专注于大数量文档的存储，而couchdb的每一处设计，都紧扣"WEB"。比如它内置的restful api，可允许客户端通过http直接操作数据库；数据采用json格式，与js程序交互时非常直接；对于数据的分发同步有很好的解决方案，可以让一个web程序offline。然而最让我眼前一亮的是，通过某些工具（如couchdb, kanso），可以把html/css/js等文件，直接嵌入到couchdb中，这时的couchdb又成了一个web server！

嵌入到couchdb中的js，可以通过某些回调，对数据进行处理，对访问进行控制。我以前还担心，完全开放了restful api的couchdb，如何保证客户端的访问在权限的控制之内，现在终于心安一些（还没有深入，不能完全心安）。这一处创新是我非常喜欢的，因为对于开放了restful api的数据库来说，额外的web层，似乎有点多余（就像是一层代理，把api再包装一下再对外）。现在好了，直接放数据库里，多省事。

可以想像，对于需求不是很复杂的信息管理系统，前台直接用js mvc+ui框架，通过restful api接口与couchdb直接交互，某些逻辑可以写在前台，重要操作写在couchdb中的js里，就可以快速地实现功能。当需求更复杂一些时，可以考虑某些web框架，如node.js+express等等。

虽然感觉这套东西不错，但是一下子这么多新东西，让我无从下手。在slideshare.net上看了不少演示，有一些概念了，但还是觉得很虚。于是我在angularjs的google group中问到，哪里有基于angularjs+couchdb的开源代码可供参考，让我倍受感动的是Peter告诉我他正在写的一个示例，以及详细的安装试用说明：[https://groups.google.com/forum/?fromgroups#!topic/angular/S5bAfaY1qP4](https://groups.google.com/forum/?fromgroups#!topic/angular/S5bAfaY1qP4)

该项目的地址为：[https://github.com/petebacondarwin/questionnaire-angular](https://github.com/petebacondarwin/questionnaire-angular)

Angularjs的google group的确是一个好地方，再一次感谢这些热心的朋友们。

我花了近一天的时间，在windows下与linux下这个示例。Linux成功了，windows下失败在最后一步，应该是kanso的一个bug：[https://groups.google.com/forum/?fromgroups#!topic/kanso/O-2dpLNTTn0](https://groups.google.com/forum/?fromgroups#!topic/kanso/O-2dpLNTTn0)

这里先解释一下提到的一些词：

1.  **angularjs** 一个浏览器端的mvc框架，双向绑定、router、directive、filter、以及对测试的良好支持，深深打动了我，是让我尝试转向js平台的最直接原因2.  **couchdb **一个基于文档的Nosql数据库，提供了很多web友好的功能，基于erlang开发3.  **nodejs** 让js运行于服务器端，它打开了js走向服务器端的大门4.  **npm** Nodejs Package Manager，nodejs的包管理器，可以方便地安装管理第三方插件5.  **express **一个轻量级的web开发框架，目前在nodejs世界应该是最为人知的6.  **kanso** 一个couchdb工具，可以把我们编写的js/css/html等文件，打包后放入couchdb，实现couchdb+web server合体功能

先看一下最终效果，再讲安装中遇到的问题及一些要点：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb.png "image")](http://freewind.me/wp-content/uploads/2012/05/image.png)

我也不知道naire这个词是什么意思，但估计这是一个“病人问卷调查”程序。首页显示一些调查问卷的名字，我们可以点击一个答题。这里点第一个：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb1.png "image")](http://freewind.me/wp-content/uploads/2012/05/image1.png)

点右边的Start开始，要先写资料及时间：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb2.png "image")](http://freewind.me/wp-content/uploads/2012/05/image2.png)

注意第一个空，有下划线表示可以填写（并且只能输入数字），中间还有横线隔开。这里用到了angularjs中的自定义directive组件的功能。下面是选择日期，看样子应该是jquery-ui的日期控件。

填好后，右边出现Next按钮，点击它，开始答题：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb3.png "image")](http://freewind.me/wp-content/uploads/2012/05/image3.png)

对于每个问题，它下方都会有几个答案供选。直接点击其中一个，出现Next，进入下一题：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb4.png "image")](http://freewind.me/wp-content/uploads/2012/05/image4.png)

全部答完后，出现汇总页，可查看并修改答案：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb5.png "image")](http://freewind.me/wp-content/uploads/2012/05/image5.png)

注意此处，第一行的数据，与后面几个明显不同。如果是使用传统数据库，它们应该在不同的表中，而couchdb中是怎么处理的呢？（下回分解）

确认无误后，点击右上角的submit，提交过去：

[![image](http://freewind.me/wp-content/uploads/2012/05/image_thumb6.png "image")](http://freewind.me/wp-content/uploads/2012/05/image6.png)

再点左边的按钮，回到了首页。

整体流程清楚了，其中“下一题”的流程，以及不同结构问答的处理（如有的是选择，有的是填空，答案个数不同），是亮点，值得好好研究。

**安装要点记录**

1.  nodejs一定要装0.6.6，不能装最新版。因为kanso只支持到0.6.6，得迁就它。2.  在linux上安装couchdb的时候，不要使用source安装，因为实在太麻烦。使用yum install couchdb，先安装一个旧版本用着。参考我另一个文章：[http://freewind.me/blog/20120501/831.html](http://freewind.me/blog/20120501/831.html)3.  在安装某些nodejs的包时（如kanso），如果不是root，需要在前面加sudo，如sudo npm install kanso -g。但它会提示找不到"sudo npm"命令，参考[http://stackoverflow.com/a/5062718/342235](http://stackoverflow.com/a/5062718/342235 "http://stackoverflow.com/a/5062718/342235")解决4.  kanso在windows上运行push命令时，会报错（路径处理问题）。该问题应该会很快解决

改日再好好研究代码，希望有人能一起研究，人多力量大。