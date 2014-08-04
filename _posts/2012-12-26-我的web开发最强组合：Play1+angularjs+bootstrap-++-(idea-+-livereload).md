title: 我的web开发最强组合：Play1+angularjs+bootstrap ++ (idea + livereload)
tags:
  - JavaScript
  - PlayFramework1
date: 2012-12-26 20:57:26
---

<table border="1" cellspacing="0" cellpadding="2" width="800">
<tbody>
<tr>
<td valign="top" width="107">2012-12-26</td>
<td valign="top" width="693">初稿，包含play1+angularjs+bootstrap+idea+livereload介绍</td>
</tr>
<tr>
<td valign="top" width="107">2012-01-21</td>
<td valign="top" width="693">添加了haxejs部分</td>
</tr>
<tr>
<td valign="top" width="107">2013-01-28</td>
<td valign="top" width="693">删除haxejs，添加typescript部分，并修改了在play1中使用angularjs的方式</td>
</tr>
<tr>
<td valign="top" width="107">2013-01-30</td>
<td valign="top" width="693">暂时不再使用typescript，因为相关工具不成熟</td>
</tr>
</tbody>
</table>

首先说明我开发web的情况：

*   个人开发*   前后端全部自己搞定*   网站类型多为传统多页面程序*   注重开发效率*   Javascritp能力不强*   美术细胞很少

由于每个人情况不同，选择技术及方案的重点也不同，所以内容仅供参考。对于我来说，这一套不论开发效率还是开发感受都是很好的。

## 一、编辑器

我选用的是idea Ultimate，它除了超强的java编辑功能之外，还提供了功能完善的play1和play2插件，angularjs插件，javascript/css编辑功能，让人开发时事半功倍。这里截几个图说明一下：

<table border="1" cellspacing="0" cellpadding="5" width="782">
<tbody>
<tr>
<td valign="top" width="234">**Play1支持**</td>
<td valign="top" width="546"> </td>
</tr>
<tr>
<td valign="top" width="234">模板标签的提示与补全</td>
<td valign="top" width="546">[![image](http://freewind.me/wp-content/uploads/2012/12/image_thumb8.png "image")](http://freewind.me/wp-content/uploads/2012/12/image8.png)</td>
</tr>
<tr>
<td valign="top" width="234">href属性中，可提示controller及action</td>
<td valign="top" width="546">[![image](http://freewind.me/wp-content/uploads/2012/12/image_thumb9.png "image")](http://freewind.me/wp-content/uploads/2012/12/image9.png)</td>
</tr>
<tr>
<td valign="top" width="234">routes文件中的提示</td>
<td valign="top" width="546">[![image](http://freewind.me/wp-content/uploads/2012/12/image_thumb10.png "image")](http://freewind.me/wp-content/uploads/2012/12/image10.png)</td>
</tr>
<tr>
<td valign="top" width="234">html中play标签的高亮和格式化</td>
<td valign="top" width="546">[![image](http://freewind.me/wp-content/uploads/2012/12/image_thumb11.png "image")](http://freewind.me/wp-content/uploads/2012/12/image11.png)</td>
</tr>
<tr>
<td valign="top" width="234">从action中快速跳转到view</td>
<td valign="top" width="546">[![image](http://freewind.me/wp-content/uploads/2012/12/image_thumb12.png "image")](http://freewind.me/wp-content/uploads/2012/12/image12.png)           
点击红框后自动打开：           
[![image](http://freewind.me/wp-content/uploads/2012/12/image_thumb13.png "image")](http://freewind.me/wp-content/uploads/2012/12/image13.png)</td>
</tr>
<tr>
<td valign="top" width="234">**Play2支持**</td>
<td valign="top" width="546">因为我不使用play2，所以未实际测试过。不过play2支持作为idea12的卖点之一，其支持程度应该不会低于play1。详情可参看idea官网上的介绍。</td>
</tr>
<tr>
<td valign="top" width="234">对于play2中scala模板的支持应该是很多人想要的</td>
<td valign="top" width="546">[![image](http://freewind.me/wp-content/uploads/2013/01/image21_thumb.png "image")](http://freewind.me/wp-content/uploads/2013/01/image211.png)</td>
</tr>
<tr>
<td valign="top" width="234">**Angularjs支持**</td>
<td valign="top" width="546"> </td>
</tr>
<tr>
<td valign="top" width="234">html属性提示</td>
<td valign="top" width="546">[![image](http://freewind.me/wp-content/uploads/2012/12/image_thumb15.png "image")](http://freewind.me/wp-content/uploads/2012/12/image15.png)</td>
</tr>
<tr>
<td valign="top" width="234">**Bootstrap支持**</td>
<td valign="top" width="546">idea没有对bootstrap有特别的支持，不过因为它本身对css的支持比较好，所以也可以方便的得到提示</td>
</tr>
<tr>
<td valign="top" width="234">class提示</td>
<td valign="top" width="546">[![image](http://freewind.me/wp-content/uploads/2012/12/image_thumb16.png "image")](http://freewind.me/wp-content/uploads/2012/12/image16.png)</td>
</tr>
<tr>
<td valign="top" width="234">**Javascript支持**</td>
<td valign="top" width="546">idea ultimate对javascript的支持非常强大，是我用过的编辑器中，对js支持最好的。不论是js文件，还是在html中嵌入的js代码，高亮、格式化、查错、提示等功能，都是一流</td>
</tr>
<tr>
<td valign="top" width="234">**css支持**</td>
<td valign="top" width="546">idea ultimate对css的支持也是非常强大</td>
</tr>
</tbody>
</table>

需要注意的是，上面提示的各功能，基本上都是idea ultimate才提供的（idea社区版中基本上没有上述功能）。如果你经济能力足够并且喜欢idea，不妨购买license对它进行支持；否则的话，自行google解决。

## 二、Live Reload

LiveReload是指当我们修改了项目中的文件时，浏览器会自动刷新，显示修改之后的效果。

在开发网站时，这个功能非常有用。想想你面前开着两个显示器，中间这个是编辑器，旁边的那个是浏览器。每当修改了java/html/css/js代码时，手不离键盘、光标不离编辑区，浏览器就自动刷新了！只需要眼睛轻轻一瞟，脖子都不用动，就能看到修改之后的效果，这种感觉何等舒畅！钛合金的F5都不需要了！

这种方式对于web框架有要求，首先是修改文件后不需要重启服务器。像纯html/php/rails都天生支持，java中某些框架支持，而play1的支持相当优秀。不论修改html/css/javascript，还是java源代码，甚至是配置文件，都不用重启，直接刷新浏览器了。刷新时间大约为1秒到4秒。

另一点是：因为livereload检查到文件修改后，只会触发浏览器刷新一次，所以要保证一次刷新就可以看到修改后的效果。某些框架利用tomcat/jetty的自动重启，无法很好的配合livereload。因为还没有重启完时，livereload就刷新了，取得的可能还是修改前的页面，必须手动刷新多次才能确定看到的是修改后的效果。对于这种情况，livereload几乎没用。而play在刷新过程中，会阻塞http请求，可以保证一次刷新就拿到修改之后的页面。

Livereload的官网是[http://livereload.com](http://livereload.com)，它支持mac/linux/windows，同时还有chrome/firefox的浏览器插件。它对windows的支持比较差，很容易崩溃，而且是收费的。所以我们只需要用它的浏览器插件就可以了（免费的），然后再找一个免费的替代器换掉服务器端。

我选择的是：[https://github.com/lepture/python-livereload](https://github.com/lepture/python-livereload)，它是一个python程序，以命令行方式启动，可以跟livereload的浏览器插件通信，效果不错。注意最好从github中下载源代码安装，因为通过pip或easy_install安装的版本有点旧，使用过程中有问题。（不清楚现在是否已经更新）

使用如下：

    cd myproject 
    cd app 
    livereload

    然后启用浏览器的livereload插件即可。

    来段gif演示一下（注意每次刷新都是我按了ctrl+s后自动触发的）:

    [![xxx](http://freewind.me/wp-content/uploads/2012/12/xxx_thumb.gif "xxx")](http://freewind.me/wp-content/uploads/2012/12/xxx.gif)

    ## 三、Why play, and why play1

    参考我写的另一个日志：[http://freewind.me/blog/20120728/965.html](http://freewind.me/blog/20120728/965.html)

    ## 四、Why angularjs

    曾经有一段时间，我对前端javascript框架很感兴趣，试用了很多，比如backbone/knockout/knockback/angularjs/...（以及一大堆已经忘了名字的），其中有两个让我印象深刻。一个是backbone，一个是angularjs。

    Backbone的优点在于学习成本很低，与jquery的思路接近，偏向于底层及手动控制。采用backbone的项目比较多，资料也比较多，社区也比较大，还有一些基于backbone发展起来的高一级的框架（如[https://github.com/backbone-boilerplate/grunt-bbb](https://github.com/backbone-boilerplate/grunt-bbb)）。这个bbb我没有用过，只是在js群中有人说使用它之后，开发效率比之前纯backbone有很大的提高，所以这里提一下。对于技术较普通的团队来说，使用backbone可能会比较保险一些。

    而angularjs则是一个让我感到惊艳的框架，相对于同类无数个mv**框架，它的优势达到了数量级。如果用几个词来形容它，应该是：学习成本高，开发效率高，写代码时思路流畅。

    它拥有双向绑定、directive、直接改写html标签等特性，使用它你可以对现有的html标签进行改进和增强，甚至还可以重写一套完全属于自己的html标签。也许你会觉得像“双向绑定”这种烂大街的特性有什么值得拿出来说的，关键在于anguarljs的设计非常统一，各个功能搭配得很流畅、一气呵成。这种感觉就像是eclipse与idea在操作上的区别：idea虽然有很多功能eclipse也提供了，但是用起来总不像idea中那么流畅 &#8212; 不论编辑什么类型的文件，在idea中都可以使用非常类似的操作，得到非常类似的界面反馈。

    Angularjs的学习成本比较高，主要原因是其设计思路与我们以前写jquery代码时有很大的不同，不能套用。Angularjs的核心思想就是“复用”，它的“复用”体现在"directive"上。Directive既是angularjs的核心，也是它的重点、难点和杀手级特性。简单的说，directive可以是一个自定义的html标签、或者属性、或者注释，它的背后是一些函数，可以对所在的html标签进行增强，比如修改html的dom内容，增强它的功能（如用第三方js插件包装它）。

    编写Directive比较复杂，需要理解它的内部原理才能定义出自己的directive。在掌握它以前，以前一些很简单的事情可能都没办法做，容易让人沮丧。比如在使用jquery时，经常会这样操作：

    $("#mydiv").dialog();

    但这种写法在使用angularjs的html页面中，是无法使用的。你必须把它写成一个directive（比如ui-dialog），然后在它的postLink()方法中，对传入的element元素操作：

    element.dialog()

    如果不理解postLink的各参数以及它是如何被angularjs使用的话，很难写出来。所以在使用angularjs的前期，很容易被卡住。

    在学习angularjs时，一定要细读官网提供的develop guide ([http://docs.angularjs.org/guide/](http://docs.angularjs.org/guide/))，把各章节读懂，知道angularjs的内部运行原理。千万别按jquery的方式学习，光看示例是绝对不够的。

    Angularjs的另一个杀手级特性，就是把流程控制、事件绑定等代码，直接写在html标签上。这其实就是前面所说的directive的使用方式。先看一段代码：

    [![image](http://freewind.me/wp-content/uploads/2012/12/image_thumb17.png "image")](http://freewind.me/wp-content/uploads/2012/12/image17.png)

    在这段html代码中，你可以看到那些位于html标签上由蓝色背影标出来的内容，都是angularjs提供的directive。有的是绑定事件（如ng-click,ng-submit），有的是控制流程（如ng-repeat）。这种方式我非常喜欢，简单直接，可读性又很好。当然有人不喜欢这种方式，认为html就应该干干净净，应该把这些东西分享到javascript中，就像下面这样：

    $("form").submit(function() { ... });

    其实对于这种情况，angularjs也有相似的做法，即为该form定义一个directive，比如my-add-form，然后把那些逻辑代码放到它里面：

    <div class="mycode">
    <form my-add-form>...</form> // js code 
    module.directive("myAddForm", function() { 
        // the logic 
    });
    </div>

    不过这种方式对于一个不那么通用的逻辑来说有点重。所以我们通常还是采用在html标签上写控制，在controller中写逻辑的方式来做，通过合理的分配，在可读性与方便性之间取得平衡。

    直接在标签上绑定事情处理函数，可以减少大量的命名，减少无谓代码，而且阅读起来更直观。想当初看backbone代码时，发现有二分之一的代码，都是通地css selector来获取元素，再将其某个事件与某个函数绑定起来。这样的代码一旦写完，html就不敢随便动了。因为若更改了html结构、id或者css class，这边的js代码都可能无法正常执行。

    熟悉angularjs以后，会发现实现前端效果时，开发效率很高。在写html的同时，基本上就可以把大部分的交互效果写出来。同时，angularjs以model为中心，在编码时只需要考虑model。当改变了model的内容时，view就会自动更新，这可以让我们需要关注的东西更少。使用了angularjs后，你会发现html标签的表现力变强了，以前需要一些js插件实现的功能（比如简单的tab、tree等），使用angularjs几行代码就可以实现，而且所有的东西都是可定制的。

    比如一个tab:

    <div class="mycode">
    <div> 
        <a ng-click="tab=1">Tab1</a> 
        <a ng-click="tab=2">Tab2</a> 
        <a ng-click="tab=3">Tab3</a> 
    </div><div ng-show="tab==1">This is tab1</div> 
    <div ng-show="tab==2">This is tab2</div> 
    <div ng-show="tab==3">This is tab3</div>
    </div>

    比如一个tree

    <div class="mycode">
    <script type="ng/template" id="'node.html'"> 
        {{node.name}} 
        <ul> 
            <li ng-repeat="node in node.children" ng-include="'node.html'"></li> 
        </ul> 
    </script><ul> 
        <li ng-repeat="node in rootNodes" ng-include="'node.html'"></li> 
    </ul>
    </div>

    我在做某个网站后台时，开始打算用angularjs，后来感觉功能比较简单，就直接采用jquery，少引用一个库。开始还好，很快就发现只要增加一点复杂的功能时，所花的时间就大大增加。如果同样的功能使用angularjs来写，会非常简单。

    ## 五、单页面程序以及前后端交互

    一般认为前端mv**框架适合于单页面程序，无刷新、局部更新的那种。前后端完全分离，之间以restful api交互，使用json交换数据。在前端做好router，当点击了某个按钮需要展示新内容时，直接由前端获取并显示另一个局部html页面，同时调用某个restful api获取json数据填充。这种程序，通常前端的功能比较复杂，而对后端要求较少。采用这类mv**框架，前端程序员们可以充分发挥自己的才智，完全使用javascript/css/html来实现功能。而对于后台，只需知道restful api接口即可。这是前端mv**推荐的方式，也是目前来说比较好的方式。其特点是“**<font color="#c0504d">以前端js框架为主，后端为辅</font>**”。

    Anguarljs对于这种方式，有着非常好的支持。它除了提供前端router外，还提供了一些与后台交互的service，如与ajax相关的$http，与restful相关的$resource；对于cookie与本地存储支持也很好，基本上使用angularjs就可以把程序做完。后台可以使用各种语言、各种框架来提供restful api。比如，我尝试过couchdb这个数据库，它直接在数据库层面提供了restful api作为外界操作数据库的接口，angularjs与它配合起来，连服务端程序都不用了。

    在开发android程序时，我也尝试过将phonegap与angularjs结合起来，直接使用angularjs来实现程序。与后台之间通过restful api交互。最后虽然因为性能要求改用了android原生方式，但对于普通的安卓或ios应用来说，这种方式是一种很好的选择，开发效率很高。

    ## 六、传统多页面程序

    对于我来说，大部分的网站还是传统多页面的。比如一个信息管理系统的后台。这种情况下能否使用angularjs呢？

    最开始的时候，我想采用单页面的方式来做，按照前面所说的流程。但是很快遇到了不少麻烦：

1.  页面跳转：这种网站页面很多，在前端定义比较麻烦，因为需要写长长的url，容易出错，检查也不方便。而使用play的模板引擎，可以直接写@{Controller.action(params)}，play会自动把它变为url，并且会检查是否有拼写错误，非常方便。2.  <strike><font color="#a5a5a5">权限：页面上某些按钮的显示与隐藏，取决于当前用户的角色。所以每次显示某个新页面时，都需要后台传过来一些json数据，例如：{ buttons: [{show: true}, {edit: false}]}，来告诉前台显示或隐藏哪些按钮。有的时候这个操作非常繁琐。</font></strike>3.  多次请求：当显示一个新页面时，可能需要多次请求。首先html模板一次，然后取json数据一次（或多次）。这样给人的感觉就有点慢。虽然可通过一些手段（如缓存html模板，为每一个请求返回一个大的合并过的json数据），但由于模板显示的时间与取得json数据的时间之间总有一些间隔，有时候还是会让人觉得不太流畅，卡。

    这几个问题我想了很久也没好办法，甚至打算放弃angularjs，还是采用以前完全由服务端生成页面的方式来做。好在最后改变了思路，“**<font color="#c0504d">以后端框架为主，前端为辅</font>**”，找到了比较好的办法。

    ### 使用play模板的继承、包含功能

    我没有像angularjs推荐的那样，采用静态的html，而继续使用play模板引擎，因为它有两大好处：

    #### 在服务器端装配好最终的html页面

    利用play的模板引擎，我们可以把模板分开为多个文件，使用#{extends/}和#{include/}等标签，来继承及包含另一个页面。这个过程是在服务器端完成的，发给前端的是一个单独的html页面。它可以减少html请求的次数，并且文件组织起来也很灵活。

    但如果我们用静态html来做，就有麻烦。如果将文件分为多个，使用ng-include包含，则它会产生一个新的html请求。这个请求将会在主页面处理完之后才请求，所以会有比较明显的延迟。另外，并且当页面分块较多时，很不方便。比如一个页面是“品”字型结构，顶端可以改变下面的页面，左边又能改变右边的页面，则在angularjs中不好实现，因为angularjs不支持嵌套的ng-view，只能用ng-include模拟，而这个过程是很繁琐的，并且可能产生更多的延迟性的请求。

    #### 可生成调用地址

    使用前端mvc框架的另一个不方便的地方，就是配置routes很麻烦，这是全手动的过程。当页面很多并且有嵌套时，更加痛苦。但如果我们使用Play模板系统，则访问一个html页面还是得通过Play的action，这样就可以在服务器端生成一个js文件，找到这些入口，把它们对应的url算出来，传给前端直接用。前端要访问一个页面，只需要这样写：

    <ul>
      <li ng-repeat="user in users"><a ng-href="{{Users.show(user.id)}}">{{user.name}}</a></li>
    </ul>

    注意其中的`User.show()`这个方法，它实际上是由后台生成的。它到底对应哪个url，在前台是完全不用操心的。哪怕以后url改了，但也丝毫不会影响页面中的代码，因为url是自动生成的。

    如果不使用Play的模板系统，很难做到这些。另外，由于我们仅用到最基本的功能，所以可以考虑采用性能更好的模板系统来代替Play的模板引擎，比如green同学的[rythm](http://rythmengine.com)。

    ### 后端生成Js文件供前端调用

    Angularjs在前台需要调用后台的方法时，往往通过一些restful api接口。由于restful api的意义在于良好、稳定的结构，让前后台解耦，但对于我来说，这点意义不大。

    所以我可以利用Play的目录结构，让Play根据action的信息，生成一个jsRoutes文件，把每个action的get/post的调用方式，以及生成url的功能都包含进去。前台的angularjs要跳转页面，或者调用一个ajax方法，都只需要调用其中的一个函数即可，不需要关心url是什么。这里在下面将详细说明。

    这种方式让事情变得简单多了，不追求无刷新，而追求开发效率与写代码时的舒适性。

    ## 七、关于angularjs的$resource

    在angularjs中提供了一个service叫$resource：[http://docs.angularjs.org/api/ngResource.$resource](http://docs.angularjs.org/api/ngResource.$resource)，它可以通过一个url和一些参数，定义一个resouce对象。通过调用该对象的某些方法，可以与后台交互，并且直接得到经过它处理之后的数据。使用的感觉有点像我们在后端常用的dao。

    这个$resource服务对于“单页面，以restful api交互”的情况比较合适。它要求所给出来的url可以按restful api的方式调用，正好满足适合这种情况。

    但对于“传统多页面程序”不好用，特别是那种信息管理系统。因为它们的url形式并不重要，是否restful也不重要，只要提供get/post两种方式，能把参数传过去就行了。如果在这里使用$resource，按它的规定来套，需要花很多心思来设计url，非常痛苦。我在这里卡了很长时间，因为我想不明白，为什么这个$resource看起来很好，但用起来就是不对劲呢。最后终于想通，原来我的情况不需要restful api。

    最后我的做法是，在服务端把各action收集起来，生成一个js文件，在这个文件里把action以js函数的方式暴露出来，供angularjs直接调用（内部使用了angularjs提供的$http服务，而没有用$resource）。在本例中，这个js文件中定义了一个叫JsRoutes的object供使用。

    Angularjs调用它的方式是这样的：

    function Ctrl($scope, JsRoutes) { 
        $scope.submit = function() { 
            JsRoutes.Users.create.post({ 
                  username: username, 
                  password: password 
            }, function(res) { 
                  alert("ok"); 
            }); 
        } 
    }

    这样，当我需要向后台传user相关的数据时，直接调用预定义的JsRoutes.Users.create即可，而不需要关注它对应的url到底是什么。

    这个js文件的代码是这样的：

    angular.module('JsRoutes', []).factory('JsRoutes', function ($http) {
        var defaultErrorHandler = function (data, status, headers, config) {
            alert('Sorry, server responses ' + status + ' error: ' + data);
        };
        // angular post json by default, change it to key value pairs
        var keyValuesTransformFn = function (d) {
            return jQuery.param(d);
        };
        var commonHandler = function (method, url, params, data, success, error, config) {
            config = config || {};
            config.method = config.method || method;
            config.params = config.params || params;
            config.data = config.data || data;
            config.url = config.url || url;
            config.timeout = config.timeout || 120 * 1000;
            var postType = config.postType || 'form';
            if (postType === 'form') {
                config.transformRequest = keyValuesTransformFn;
                config.headers = config.headers || {};
                config.headers['Content-Type'] = 'application/x-www-form-urlencoded; charset=UTF-8';
                // config.headers['Accept'] = "application/json, text/html, text/plain, */*";
            }
            $http(config).success(success).error(error || defaultErrorHandler);
        };
        var jsRoutesHandler = function (path) {
            return {
                get: function (params, success, error, config) {
                    commonHandler('get', path, params, {}, success, error, config);
                },
                post: function (data, success, error, config) {
                    commonHandler('post', path, {}, data, success, error, config);
                },
                link: function(params) {
                    return path + "?" + jQuery.param(params);
                }
            }
        };

        return {
            #{list actions.keySet(), as: 'module', separator: ','}
                #{if module}
                    ${module} : {
                        #{list actions.get(module).keySet(), as: 'controller', separator: ','}
                        ${controller} : {
                            #{list actions.get(module).get(controller), as: 'item', separator: ','}
                                ${item.action}: jsRoutesHandler('${item.path}')
                            #{/list}
                        }
                        #{/list}
                    }
                #{/if}
                #{else}
                    #{list actions.get(module).keySet(), as: 'controller', separator: ','}
                    ${controller} : {
                        #{list actions.get(module).get(controller), as: 'item', separator: ','}
                            ${item.action}: jsRoutesHandler('${item.path}')
                        #{/list}
                    }
                    #{/list}
                #{/else}
            #{/list}
        }
    });

    注意其中的get/post/link三处，这是前台调用它们的关键方法。（现在使用typescript后，还可以考虑生成typescript的声明文件）

    采用这种方式后，在前端js代码中完全不需要跟url打交道，因为它们都是在服务器端根据routes文件生成的。以后url有什么变化，js这里不需要修改一行代码。

    另外需要注意的是，angularjs默认会向服务端发送json格式的数据，而play对key-value形式的数据处理的比较好，所以我就把它默认值改为了`'application/x-www-form-urlencoded'`

    ## 八、Angularjs中如何集成第三方js插件

    有时候需要用一些第三方插件，比如datepicker，slider，或者tree等。以前的做法是直接通过jquery取得某个元素，然后调用某个方法即可。但在angularjs中，不能直接这么写，必须写在directive中。

    有一个叫augular-ui的项目：[https://github.com/angular-ui/angular-ui](https://github.com/angular-ui/angular-ui)，已经集成了一些常用的插件（来自jqueryui），很方便。但如果还是需要自己定义，该怎么做呢？

    基本的思路就是，创建一个directive，把调用jquery插件的代码放在它里面。这里以jqueryui的slider为例：

    [![image](http://freewind.me/wp-content/uploads/2012/12/image_thumb18.png "image")](http://freewind.me/wp-content/uploads/2012/12/image18.png)

    样子如上，其示例在：[http://jqueryui.com/slider/](http://jqueryui.com/slider/)

    在jquery中，它的调用方式是这样的：

    <div class="mycode">
    <div id="slider"></div>$("#slider" ).slider({ 
        min: 0, 
        max: 100, 
        value: 10, 
        step: 5    
    });
    </div>

    而在angularjs中，我们需要定义一个directive，假设是ui-slider：

    <div class="mycode">
    app.directive('slider', function () { 
        return { 
            require: '?ngModel', 
            restrict: 'A', 
            link: function (scope, element, attrs, ngModel) { 
                var opts; 
                opts = angular.extend({}, scope.$eval(attrs.slider)); 
                var slider = element.slider({ 
                    min: opts.min || 0, 
                    max: opts.max || 100, 
                    step: opts.step || 10, 
                    value: attrs.ngModel &amp;&amp; scope.$eval(attrs.ngModel) || 50, 
                    slide: function (event, ui) { 
                        if (ngModel) { 
                            scope.$apply(function () { 
                                ngModel.$setViewValue(ui.value); 
                            }) 
                        } 
                    } 
                }); 
                scope.$watch(attrs.ngModel, function (v) { 
                    slider.slider({ 
                        value: v 
                    }); 
                }); 
            } 
        }; 
    });
    </div>

    在html中的调用方式是：

    <div slider="{min:0,max:500,step:5}" ng-model="row.width"></div>

在directive中的代码比jquery的多了不少，不过主要是增加了与双向绑定有关的两个函数。例如在"slide: function(event, ui)“中，是把sider的值传回给某个model（本例中为row.width）。在后面的scope.$watch中，是把model的值传给slider。这样当拖动slider上的刻度时，row.witdh的值会自动改变；或者改变了row.width的值之后，slider的刻度也会自动变化。

基本的思路就是这样，更详细的需要看相关文档。与angularjs相关的就讲到这里，在这里你可以看到很多angularjs的可执行的例子：[https://github.com/angular/angular.js/wiki/JsFiddle-Examples](https://github.com/angular/angular.js/wiki/JsFiddle-Examples)，可以直接感受。

最后需要补充的两点：

1.  Angularjs的社区气氛很好。其google group人气很旺，有问题很快就能得到详细的回复2.  Angularjs对IE6/7支持不好。如果一定要支持ie6/7，可考虑backbone或其它框架。

## 九、Bootstrap

Bootstrap是像我们这样缺少艺术细胞的程序员的福音。只需要记一些css class和某些js component的用法，就可以做出看起来比较美观、专业的页面效果出来。虽然过不了多久，人们也许就会对它出现审美疲劳，但好在它的社区已经形成，已经有不少基于它的模板出来，相信以后会有更多更美观的bootstrap theme可供选择：

*   [https://wrapbootstrap.com/](https://wrapbootstrap.com/)*   [http://bootswatch.com/](http://bootswatch.com/)

## <strike>十、Why HaxeJs</strike>

<strike>我的Js能力不强，主要是因为觉得Js里陷阱太多，总是觉得不安。所以我一直想找一个有静态类型、功能强大、能生成js并且生成的代码体识较小的语言，来代替它。经过多次尝试，终于让我找到了。</strike>

<strike>它就是HaxeJs。参看：</strike>[<strike>我为什么选择haxejs，而不是dart/typescript</strike>](http://freewind.me/blog/20130122/2019.html)

<strike>HaxeJs对于我来说，意义是很大的，因为Javascript是我长久以来心中的痛。不想用，但经常又不能不用，所以我尽量把逻辑写在后台，前台通过Ajax调用。但对于页面效果，不写JavaScript是不行的，所以我总为这犯愁。使用了Angularjs后，虽然对于javascript的要求大大降低，但当逻辑复杂的时候，上百行的js代码还是让我很不安。</strike>

<strike>但现在有了haxejs，我可以在一种类型安全的环境中写js代码，感觉就安全不一样了。以后我可以把更多的逻辑放在前台由js实现，或者使用haxe来写后台（比如nodejs/php等），感觉脚下的路一下子变宽了。</strike>

经过几天的试用，我[发现Haxejs与Angularjs之间不合适](http://freewind.me/blog/20130126/2022.html)

## <strike>十、Why Typescript</strike>

<strike>在haxejs之后，又尝试了由微软推出的typescript。它的语言特性没有haxejs那么强，但是正好避开了haxejs与angularjs之间不匹配的那几个问题：</strike>

1.  <strike>Typescript的思路还是javascript，并且兼容js语法，angularjs再侵入html也不怕 </strike>
2.  <strike>Typescript中允许使用$作为变量名 </strike>
3.  <strike>Typescript的类型功能基本可用，并且可下载到Angular的声明文件 </strike>

<strike>详细内容可参考我的另一个文章：</strike>[<strike>使用Typescript来写javascript</strike>](http://freewind.me/blog/20130128/2034.html)

经过几天的试用，Typescript倒在了工具和语言本身还不够成熟：[使用Typescript来写javascript (目前相关工具不够成熟)](http://freewind.me/blog/20130128/2034.html)

等到idea能支持直接在编辑器中实时检查错误时，再使用。