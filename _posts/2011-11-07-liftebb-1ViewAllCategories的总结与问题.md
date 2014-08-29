---
layout: post
title: lift.ebb 1.ViewAllCategories的总结与问题
tags: 未分类
date: 2011-11-07 17:39:30
---

Ebb的第一个use case，是view all categories，即查看所有的论坛组。先看看usebb的首页是什么样的：

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb1.png "image")](http://freewind.me/wp-content/uploads/2011/11/image1.png) 

再看我现在做成的样子：

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb2.png "image")](http://freewind.me/wp-content/uploads/2011/11/image2.png) 

虽然样子有点丑，但是好歹数据已经从数据库里取出来了。最关键的是，对lift的恐惧感大大减小，隐约开始理解它的运行方式了。

这是在教主的领导之下，使用他的rup开发方式，实现的第一个use case。我本打算先看几天的lift，差不多熟悉了再动手，但是教主说，让我直接在他的ebb代码基础上做，先照葫芦画瓢，做出功能后，再想着改进和学习。

<span id="more-561"></span>
<p>**总结：**

一、现在的liftweb (lift2.4m4) 只支持到2.9.0.1（以及2.8.1)，不支持最新的2.9.1。这个项目，使用的是2.8.1

二、liftweb使用的还是sbt 0.7.x，而不是最新的0.10.x。这个项目，使用的是0.7.5

三、配合jrebel，可以将修改代码生效的时间由10秒左右，减小到4秒。

四、如何在lift中使用jrebel，将另写一篇文章

**关于lift的理解**

我用过很多mvc类框架及wicket/tapestry这样的非mvc，但初看lift代码时，还是觉得摸不着头。因为在lift的代码中，有很多奇怪的符号及独特的渲染方式：类似jquery的css selector替换。

lift里面东西很多，这次只接触到snippet，所以只总结这一点。

在mvc中，请求一个页面的流程是这样的：一个请求到来后，首先被router获取。它根据一定的规则，将它转发给某个controller中的某个方法(action)。在action中，可以取得request中的参数，然后根据需求，调用其它的类或方法，得到所需数据，传给view。view使用这些数据，渲染出最终的html，发给浏览器。可以看到，在这里controller是老大，它负责取数据，丢给view小弟处理。

在lift中，是反过来的。当请求到来后，首先找到的是view。比如我请求[http://localhost/abc.html](http://localhost/abc.html)，它会首先在webapp下找到abc.html这个文件。abc.html是一个正常且完整的html，不同之处在于，它里面有一些html标签，及奇怪的class属性（见后面的例子）。这些class实际上是一些声明，告诉lift如何处理、替换或填充这部分模板。lift将调用对应的snippet类（也可以看作是Component），改变模板内容，变成最终的html。可见这里，view是老大，它根据自己的模板内容，召唤snippet小弟过来给自己化妆打扮。所以lift这种方式，叫做View-first。

见这个html页面index.html

> <div id="main" class="**<font color="#ff0000">lift:surround?with=default;at=content</font>**">      
>   <h1>bbs</h1>      
>   <!- cat_header ->      
>   <table class="**<font color="#ff0000">lift:ForumSnippet.forumlist</font>** maintable">
> 
>     <tr class="t-cat_header">     
>       <td colspan="5" class="forumcat"><a href="{cat_url}" name="{cat_anchor}" rel="nofollow">&raquo;</a> <span name="cat_name">{cat_name}</span></td>      
>     </tr>      
>     <tr>      
>       <th class="icon"></th>      
>       <th>{l_Forum}</th>      
>       <th class="count">{l_Topics}</th>      
>       <th class="count">{l_Posts}</th>      
>       <th class="lastpostinfo">{l_LatestPost}</th>      
>     </tr> 
> 
>     <!- forum ->     
>     <tr class="t-forum">      
>       <td class="icon"><img src="{img_dir}{forum_icon}" alt="{forum_status}" /></td>      
>       <td>      
>           <div class="forumname">{forum_name}</div>      
>           <div class="forumdescr">{forum_descr}</div>      
>       </td>      
>       <td class="count"><div class="total_topics">{total_topics}</div></td>      
>       <td class="count"><div class="total_posts">{total_posts}</div></td>      
>       <td class="lastpostinfo">      
>           <div class="latest_post">      
>               <a href="??????"><span name="latest_post">{latest_post}</span></a>      
>           </div>      
>           <div class="by_author">By:       
>               <a href="??????"><span name="by_author">{by_author}</span></a>      
>           </div>      
>           <div class="on_date">On: <span name="on_date">{on_date}</span></div>      
>       </td>      
>     </tr>      
>   </table>      
> </div>
> 
>  

首先直接用浏览器打开这个文件，看是什么样子：

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb3.png "image")](http://freewind.me/wp-content/uploads/2011/11/image3.png) 

可见的确是一个很像“模板”的html页面。为什么经过lift渲染之后，会变成这样呢？

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb4.png "image")](http://freewind.me/wp-content/uploads/2011/11/image4.png) 

关键就在于代码中那两处红色的class。

１、**<font color="#ff0000">lift:surround?with=default;at=content</font>**

这句话是告诉lift，先找到templates-hidden/**<font color="#ff0000">default</font>**.html这个布局文件，然后用index.html的内容，替换default.html中的<lift:bind name="**<font color="#ff0000">content</font>**" />。

templates-hidden/default.html内容如下：

> <html xmlns="[http://www.w3.org/1999/xhtml"](http://www.w3.org/1999/xhtml") xmlns:lift="[http://liftweb.net/"](http://liftweb.net/")>      
>   <head>      
>     <meta http-equiv="content-type" content="text/html; charset=UTF-8" />      
>     <meta name="description" content="" />      
>     <meta name="keywords" content="" />      
>     <title>kkkk</title>      
>     <script id="jquery" src="/classpath/jquery.js" type="text/javascript"/>      
>   </head>      
>   <body>      
>     **<font color="#ff0000"><lift:bind name="content" />         
> </font>**  </body>      
> </html>

查看index.html最终生成的html源代码，里面的确用到了default.html中的代码：

[![image](http://freewind.me/wp-content/uploads/2011/11/image_thumb5.png "image")](http://freewind.me/wp-content/uploads/2011/11/image5.png) 

２、**<font color="#ff0000">lift:ForumSnippet.forumlist</font>** 

这句话告诉lift，使用code.snippet.**<font color="#ff0000">ForumSnippet.forumlist</font>**()方法来处理其所在的table标签（及内部标签）。ForumSnippet是什么样子呢？见下面代码：

> object ForumSnippet {     
>   def forumlist: CssSel =      
>     "*****" **<font color="#800080">#></font>** (for (cat <- cats) yield {      
>       "**<font color="#ff8040">.t-cat_header</font>**" #> ("@cat_name" #> cat.name) &      
>       "**<font color="#ff8000">.t-forum</font>**" #> ( for {      
>           f <- cat.forums      
>           lastTopic <- topics.lookup(f.last_topic_id)      
>       } yield {      
> 
>         val lastPost = posts.lookup(lastTopic.last_post_id).get      
>         val lastPoster = members.lookup(lastPost.poster_id).get      
>         val lastTopicTitle = (if(lastTopic.count_replies>1) "Re: " else "") + lastTopic.topic_title 
> 
>         "**<font color="#ff8000">.forumname *</font>**" #> f.name &      
> 
>         ".forumdescr *" #> f.descr &     
>         ".total_topics" #> f.topics &      
>         ".total_posts" #> f.posts &      
>         ".latest_post *" #> ("@latest_post" #> lastTopicTitle) &      
>         ".by_author *" #> ("@by_author" #> {lastPoster.name}) &      
>         ".on_date *" #> ("@on_date" #> new SimpleDateFormat().format(lastPost.post_time)) 
> 
>       })     
>     })      
> }

这段代码中，有很多奇怪的符号。但如果你使用过jquery的话，可能就会猜到：它首先将html模板（即<table>xxx</table>这一段）解析为一个xml树，然后使用css selector定位到对应的节点，最后使用实际数据去替换内容。

首先需要了解的是紫色的**<font color="#800080">#></font>**符号，它在代码中大量使用，意思是replacedWith，即用后面的数据替换左边对应的节点内容。

例如：

> ".forumdescr *" #> f.descr

 左边是css selector，".forumdescr"表示class=".forumdescr"节点，".forumdescr .aaa"表示".forumdesc"下的所有".aaa"节点，".forumdescr *"表示".forumdescr"下的所有节点。

这句代码表示将".forumdescr"下的所有内容替换为f.descr。

每行最后的`&`，表示将多个操作连接起来。

**如何替换属性？**

> <font style="background-color: #ffffff">".forumname [href]" #> [http://google.com](http://google.com)</font>
