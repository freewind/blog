title: 使用Typescript来写javascript (目前相关工具不够成熟)
tags:
  - JavaScript
date: 2013-01-28 14:07:37
---

<table border="1" cellspacing="0" cellpadding="2" width="800">
<tbody>
<tr>
<td valign="top" width="161">2013-01-28</td>
<td valign="top" width="639">初稿</td>
</tr>
<tr>
<td valign="top" width="161">2013-01-30</td>
<td valign="top" width="639">增加目前工具不够成熟的部分</td>
</tr>
</tbody>
</table>

前几天尝试使用haxejs来写javascript，以获得静态类型带来的益处。虽然成功了，但很快发现[将它与angularjs一起使用，有一些不太顺畅的地方](http://freewind.me/blog/20130126/2022.html)，导致开发效率没有提升，反而下降了。虽然我认为使用haxejs来写普通的js（或者与jquery相关的js）没有问题，但不适合与angularjs这样与HTML侵入较大的js框架配合。

昨天偶然发现idea居然支持typescript了，于是打算尝试一下typescript，目前的感觉还不错，相比haxejs，它与angularjs之间的配合要流畅得多。

## 与coffeescript的比较

Typescript与Coffeescript都是对javascript的改进，但两者走的是不同路线。Coffeescript是从语法的角度，通过提供类似于python/ruby的语法，让代码写起来更加简洁，可读性更好。并且它提供的一些控制结构，可以避开Javascript中的问题，比如`for ... in ...`，使用coffeescript可以让多层嵌套看起来不那么痛苦：

    self.validate json, (err, json) ->
      if err then cb(err)
      else self.mapFiles json, (err, json) ->
        if err then cb(err)
        else self.addFields json, (err, json) ->
          if err then cb(err)
          else self.store.create json, cb

    回调的参数放在右边，看起来就像是把前面函数的返回值放在了右边供调用，读起来比较轻松。习惯于python/ruby的开发者可能会比较喜欢coffeescript，我也觉得它在这方面很好。

    而Typescript走的是另一条路，通过增加静态类型，提高程序的可靠性，并没有从语法层面进行大的改进。

    我觉得它们两者是互补的，如果能把两者结合起来，既提供静态类型，又增强语法就太好了，不过这可能在很久以后才有可能出现。

    ## 编辑器支持

    Idea并没有声明已经支持typescript，没有看到相关插件，在建项目时也没有任何提示，只有在创建一个以.ts结尾的文件时，它可以识别出来，说明这还是一个实验性的功能，没有完全完成。不过经过我的试用，基本功能都有了：

1.  代码高亮、格式化2.  代码提示3.  错误检查

    其中编译为js文件的功能似乎有点问题，另外module()函数的检验有时候也不太对，不过这并不影响我们的使用。尽管还是一个开发中的功能，但已经跟idea对haxe的支持不相上下了。

    另外，你还可以使用VS2012+typescript插件，这是官方推荐的。另外喜欢用sublime text2的同学，可到这里下载：[https://github.com/raph-amiard/sublime-typescript](https://github.com/raph-amiard/sublime-typescript)。另外vim/emacs也有相关的插件也有，eclipse这里似乎还没有动静。

    据说typescript提供了一些服务性质的api，可以让IDE实现功能（如代码提示等）更加容易。

    ## 下载地址

    官方地址：[http://www.typescriptlang.org](http://www.typescriptlang.org)

    先到nodejs网站下载并安装nodejs，然后运行以下命令：

    npm install -g typescript

    其中npm是由nodejs提供的包管理工具，安装nodejs后就直接可用了。

    ## 相关资料

    Typescript相关的资料不多，目前官网上仅有一些示例和简单的文档，我收集的有以下这些链接

1.  官网：[http://www.typescriptlang.org/](http://www.typescriptlang.org/)[[示例]](http://www.typescriptlang.org/)[[在线尝试]](http://www.typescriptlang.org/Playground/)[[官方例子源代码]](http://typescript.codeplex.com/SourceControl/changeset/view/258e00903a9e#samples/greeter/README.txt)2.  语言规范：[http://www.typescriptlang.org/Content/TypeScript%20Language%20Specification.pdf](http://www.typescriptlang.org/Content/TypeScript%20Language%20Specification.pdf)，可用来参考，很难看懂

    ## 语言特性

    Typescript官方的文档很简单，只给出了一个简单的例子，没有详细的文档来讲解各功能，所以对于初学者入门还是稍有难度，我花了不少时间才基本会用，比我预想的时间多了很多。

    难度不是在语言特性上，而是在代码的组织和示例上，实在太少。比如如何调用声明文件，如何按module来组织代码等，对于初次接触typescript的人还需要一些时间理解。

    我对Typescript的印象，可总结为以下几点：

1.  Typescript的思路还是Javascript，只是对它进行了增强2.  Typescript可以看作是Javascript的超集，所有javascript代码可以直接写在typescript中，这一点对于Javascript程序员来说很方便3.  Typescript中增加了静态系统，提供了class/interface/method的支持。类型系统可以让我们在编译期验证代码是否有笔误，也可以获得编辑器的提示4.  Typescript以module方式来组织代码，既支持commonjs的方式，又支持amd的方式5.  Typescript中的文件可分为实现文件和声明文件，有点像c++中的.cpp和.h的关系。6.  Typescript即可用来写后台代码，也可以用来写前台代码，初步感觉似乎更偏后台一些

    ### Class/Interface/Method

    Typescript中提供了class、interface关键字，可将代码以类的方式组织在一起。构造函数为constructor，静态方法前要加static。在实例方法中调用静态方法时，必须在前面加上类名。这块看起来比较普通，很好理解。

    class User {
        constructor(name:string, age:number) {
        }
        hello() {
            alert("hell, " + name);
            User.test(age);
        }
        static test(n:number) {
            console.log("this is a static method, argument: " + n);
        }
    }

    ### module

    这块对于非Javascript程序员来说，有点不太好理解。我之前虽然学习了一段时间的nodejs，但现在已经快忘光了，这几天拾起来的时候，还是很痛苦的，资料很少。现在只能算是有所了解，讲的可能不全对。

    Javascript在语言层面没有提供模块机制，当代码多了以后，很难组织起来。比如在服务器端，将不同的功能分在不同的文件中以复用，或者在浏览器端，想把一个大文件分成多个，按需下载。但文件之间依赖关系、如何向外暴露对象供使用等，都没有规定。所以人们做了很多尝试，制定了一些规范来解决这个问题，其中比较有名的有两个名词，一个commonjs，一个是amd。

    关于它们的介绍和相互关系，可以看这篇文章，说得很好：[http://www.udpwork.com/item/3978.html](http://www.udpwork.com/item/3978.html)

    简单地说，commonjs是一套规范，它定义了如何将代码组织为模块，如何向外暴露对象，如果依赖。在导入模块时，又可以分为同步和异步，一般可认为commonjs代表同步，AMD代表异步。服务器端代码更需要同步，浏览器那边更需要异步，在typescript的编译器中同时支持这两种方式。但commonjs是默认的，所以我前面说感觉它更偏向后端。

    当我们在代码中使用了module()函数时，Typescript可以把它们编译为commonjs或amd方式的调用。比如：

    /// <reference path="./libs/underscore.d.ts"/>
    import _ = module("underscore");

    当我们这样编译时：

    tsc test.ts

    它会生成这样的js代码：

    var _ = require("underscore");

    当我们指定为amd时：

    tsc --module amd test.ts

    它会生成这样的代码：

    define(["require", "exports", "underscore"], function(require, exports, _____) {
        var _ = _____;
    });

    在服务器端我们一般用前者，在浏览器端一般使用后者（或者完全不用module）。

    ### 不使用Module

    如果我们在typescript使用了module函数，则生成的代码在浏览器端执行时，需要有一些script loader的支持。对于浏览器端代码，我们一般生成amd风格的代码，所以需要找一个支持amd的库放在前端。这样的库有很多，比如：

1.  RequireJS2.  Nodules3.  JSLocalnet4.  curl.js

    可根据自己的需要使用。我尝试了[RequireJS](http://requirejs.org)，不喜欢它的网站风格，写了那么多，但总是没说重点。比如关于它的配置，我们需要面对的第一个问题，可是它就是没给一个示例出来，让我在网上到处找别人写的例子。要想用好它，可能得先好好读它的文档，再到网上找别人的代码看。

    所以最后我还是按照传统的方式来组织代码，在typescript中完全不使用module函数，而用了全局声明的方式：

    /// <reference path="../../libs/underscore.browser.d.ts"/>
    declare var _:UnderscoreStatic;

    而不是

    /// <reference path="../../libs/underscore.browser.d.ts"/>
    import _ = module("underscore")

    当使用`declare var`来声明某变量时，即假设它已经在全局中可用，后面的`UnderscoreStatic`则是在`underscore.browser.d.ts`这个文件中定义的，关于underscore提供的所有方法的接口描述。

    使用这种方式时，我们保证这段js在浏览器端运行时，已经导入了underscore.js。我们可以按照传统的方式来引用js文件：

    <script src=".../jquery.js"></script>
    <script src=".../underscore.js"></script>
    <script src=".../myapp.js"></script>

    ### Headjs

    如果你既不想用requirejs等库，又想异步下载js文件，可以考虑这个库：[http://headjs.com/](http://headjs.com/)

    headjs不支持commonjs/amd，它有自己的异步下载的api，十分简洁好用。配合我上面的declare var方式，很好用。

    这里给出一个简单的例子（headjs+jquery+underscore+angularjs+本站js）：

    <script src="/public/javascripts/head-0.99.min.js"></script>
    <script type="text/javascript">
        head.js('/public/libs/jquery-ui-1.8.24/js/jquery-1.8.2.min.js',
                '/public/libs/jquery-ui-1.8.24/js/jquery-ui-1.8.24.custom.min.js',
                '/public/libs/underscore/1.4.3/underscore.min.js',
                '/public/libs/bootstrap-2.1.1/js/bootstrap.min.js',
                '/public/libs/angular-1.0.2/angular.min.js',
                '/public/libs/angular-ui-0.3.2/angular-ui.min.js',
                '/public/libs/marked.js',
                '/public/libs/slickswitch/js/jquery.slickswitch.js',
                '/public/libs/html5.js',
                '/public/libs/flot/0.7/jquery.flot.js',
                '/jsRoutes.js',
                '/public/libs/moment/1.7.2/moment.min.js',
                '/public/javascripts/angular-config.js',
                '/public/javascripts/app.js');
        head.ready(function () {
            app.value("AdminCommonData", {
                "menuTree": [],
            });
        });
    </script>

    在`head.js()`方法中，可传入多个js路径，它们并行下载，但按照声明顺序依次执行。可以把一个head.js()分开写成多个。`head.ready()`方法将会在所有js下载完成后执行里面的回调函数。

    headjs有一个非常好用的特性，即可以把head.js()放在head.ready()的后面：

    head.js(".../a.js"); 
    head.ready(function() { console.log('I'm ready')}); 
    head.js(".../b.js");

    其中的head.ready()函数，虽然写在"b.js"前面，但还是会等到b.js下载并执行完后，才会执行。

    如果我们的模板引擎使用了继承关系，则该特性很有用。比如在play中有一个layout.html文件和内页main.html，我们可以这样组织代码。

    layout.html

    <html>
      <head>
        <script src="/public/javascripts/head-0.99.min.js"></script>
        <script type="text/javascript">
          head.js('/public/libs/jquery-ui-1.8.24/js/jquery-1.8.2.min.js'
                  // 所有全局通用的js放在这里);
        </script>
      </head>
      <body>
        #{doLayout /}
      </body>
    </html>
    <script>
    head.ready(function() {
      // 最后执行的启动代码放在最后
    });
    </script>
    

    main.html

    #{extends "layout.html" /}
    <script>
    head.js(".../main.js",
       // 仅在本页中使用的js文件)
    </script>
    <div>
    ...
    </div>
    <script>
    head.ready(function() {
      // 可以在这里为layout.html最后的函数调用准备数据
    });
    </script>

    在这里要补充一句，如果你使用angularjs+headjs的话，不能在<html>上声明ng-app="xxx"，而应该在layout.html中最后的函数中调用：

    <script type="text/javascript">
        head.ready(function () {
            angular.bootstrap(document, ["MyModule", "MyAnother"]);
        });
    </script>

    注意一定要把<html>上的ng-app去掉。我之前尝试把它们两个结合使用总是失败，这次终于找到原因。

    ### 声明文件

    如果你仔细看了前面的例子，会发现有一些以三个斜杠开头的代码，如：

    /// <reference path="../../libs/underscore.browser.d.ts"/>

    它是一种注释，告诉typescript编译器，当前文件使用了哪些声明文件，以帮助编辑器提示信息，及编译器检查类型。这种注释很重要，如果后面的路径不对，则编译会失败。

    引用的文件以`.d.ts`结尾，它们是一种声明文件，就像是c语言中的header文件，只包含了类或函数的签名，而没有实际内容，用于编辑器提示和编译器验证。它们的内容形如：

    declare interface UnderscoreVoidListIterator {
        (element : any, index : number, list : any[]) : void;
    }

    declare interface UnderscoreMemoListIterator {
        (memo : any, element : any, index : number, list : any[]) : any;
    }

    declare interface UnderscoreListIterator {
        (element : any, index : number, list : any[]) : any;
    }

    这种文件很重要，因为我们要想使用第三方的js库，一般都需要手动做出这样的声明文件，才能在typesafe的环境中使用它们。只需要以注释的方式写上即可，不需要在实际代码中声明或引用什么。

    现在在idea中，这些引用还得手动去写，不太方便，我想tsc或者编辑器应该会对它进行增强。

    #### 下载第三方js库的声明文件

    现在有很多优秀的第三方js库，我们要在typescript中使用它们，难道要一一手动创建这些文件吗？这个工作量可不小。

    好在已经有人这么做并把成果开源出来了，我们可以直接下载它们，放在自己的项目中，再加上引用注释即可。

    包含几乎全部的：[https://github.com/borisyankov/DefinitelyTyped](https://github.com/borisyankov/DefinitelyTyped "https://github.com/borisyankov/DefinitelyTyped")，你应该把它clone到本地。

    使用方法：把它们clone到本地，放在某个地方（比如工具目录中），然后在我们自己的typescript中添加以一个引用即可：

    /// <reference path="../../libs/AngularTS.module.d.ts"/>
    /// <reference path="../../libs/underscore.browser.d.ts"/>

    #### 创建自己的声明文件

    如果我们用typescript写了一些模块，想让别人调用，除了把整个代码复制给他以外，还可以生成一个声明文件(`.d.ts`)，让他使用该声明文件即可。tsc提供了选项让我们生成.d.ts:

    tsc --declaration my.ts

    如果一切正常，将会在当前目录下产生一个`my.d.ts`文件，里面包含了my.ts中定义的代码的接口声明。

    ### 类型

    Typescript在javascript的基础上提供了类型系统，但它同时也支持动态类型。如果我们把一个变量或者返回值声明为`any`，则它表示“动态类型”，编译器不会检查它的类型信息，但我们也得不到编辑器的提示信息。

    #### any

    function(obj: any) { 
       obj.non_exist_method(); 
    }

    如代码中所示，obj被声明为any，虽然内部调用了一个不存在的方法，编译器也不会提示有误，只有在运行期才知道。

    #### 推断

    虽然typescript支持静态类型，但我们并不需要像java那样，在每个地方都要声明类型，因为typescript可以推断：

    var name = "Freewind";

    则name会被认为是string类型.

    function myname(name:string) { 
       return name; 
    }

    则myname函数的返回值被认为是string类型。

    但我们在声明的地方，最好还是加上类型，以后看起来会比较清楚。

    #### 常用类型

    <table border="1" cellspacing="0" cellpadding="2" width="800">
    <tbody>
    <tr>
    <td valign="top" width="256">any</td>
    <td valign="top" width="544">可表示动态类型</td>
    </tr>
    <tr>
    <td valign="top" width="256">string</td>
    <td valign="top" width="544">字符串</td>
    </tr>
    <tr>
    <td valign="top" width="256">number</td>
    <td valign="top" width="544">数字</td>
    </tr>
    <tr>
    <td valign="top" width="256">bool</td>
    <td valign="top" width="544">true或false</td>
    </tr>
    <tr>
    <td valign="top" width="256">null</td>
    <td valign="top" width="544">null</td>
    </tr>
    <tr>
    <td valign="top" width="256">undefined</td>
    <td valign="top" width="544">undefined</td>
    </tr>
    <tr>
    <td valign="top" width="256">void</td>
    <td valign="top" width="544">void</td>
    </tr>
    <tr>
    <td valign="top" width="256">string[]</td>
    <td valign="top" width="544">字符串数组</td>
    </tr>
    <tr>
    <td valign="top" width="256">{a;b;}</td>
    <td valign="top" width="544">等于{a:any; b:any;}</td>
    </tr>
    <tr>
    <td valign="top" width="256">{ a:string, b: number; }</td>
    <td valign="top" width="544"> </td>
    </tr>
    <tr>
    <td valign="top" width="256">{ a:string, ()=>number; }</td>
    <td valign="top" width="544">后面那个是函数</td>
    </tr>
    <tr>
    <td valign="top" width="256">() => void</td>
    <td valign="top" width="544">表示形如 function() {} 这样的函数</td>
    </tr>
    <tr>
    <td valign="top" width="256">(string) => number</td>
    <td valign="top" width="544">表示形如 function(name:string) { return 10; } 这样的函数</td>
    </tr>
    <tr>
    <td valign="top" width="256">{ [string]: number; }</td>
    <td valign="top" width="544">表示一个object，它的key为string，值为数学，形如：

    { "aaa": 111, "bbb": 222}</td>
    </tr>
    </tbody>
    </table>

    ## 语法糖

    this.filter((todo: Todo) => todo.get('done'));

## 使用grunt自动编译typescript

如果你使用的编辑器还不支持自动编译typescript，可以使用grunt来编译，还可以进行更多的任务，如对产生的js进和合并、压缩等工作，十分方便。

具体可参看这篇文章：[在Java项目中拥抱Nodejs — 使用gruntjs编译typescript，并将生成的js合并、压缩](http://freewind.me/blog/20130127/2027.html)

## Typescript待实现的特性

仅以目前的typescript来说，虽然在类型方面做的不错，但是语言本身还有很多可以改进的地方。比如简化语法，提供更好用的控制结构，对异步进行更好的支持等。如果可以在语言层面改进javascript那些容易出错、不方便的地方，我想会有更多人采用typescript。

[从这里的issue列表中](http://typescript.codeplex.com/workitem/list/basic?field=Votes&amp;direction=Descending&amp;issuesToDisplay=Open&amp;keywords=&amp;emailSubscribedItemsOnly=false)，可以看到呼声较高的特性有：

1.  支持泛型2.  实现await关键字：可以让异步调用变得简单3.  for ... of：js内置的for(x in y)对于数组来说，容易产生问题，但我们又需要经常遍历数组，所以提供for of代替4.  内置压缩js的功能5.  protected关键字6.  生成xml格式的文档7.  extension methods：可以让我们的函数调用更加流畅，比如underscore的那些方法 ，就可以写成 array.filter(function(item) {return ..})8.  allow module.exports9.  string interpolatio and block strings10.  support for abstract classes11.  macros：利用macro，可以扩展语言本身

其中我对2,3,6,10,11这几项很感兴趣，如果它们实现了，则typescript的吸引力会大大增强。

## TypeScript展望

对于Typescript的未来，我还是比较看好的，因为对于服务器端的编程，类型系统是很重要的，可以让我们的代码质量变得更高，让不同版本之间的库的兼容性也更好。

我之前使用nodejs感觉很郁闷的一点是，某一个库升级了（如改变了api接口），则相关的库都出错了，而想要找出问题很难，只能通过单元测试找到问题，再查看文档解决。而这样的问题在java中出现的就比较少，因为有类型系统的保证，如果接口改变了，直接编译都会出错。

使用typescript后，让nodejs代码也具有的这样的能力，对于社区的前进是很有帮助的。而且对于java/c#程序员来说，这是很有吸引力的。 

随着以后typescript相关编辑器、工具的成熟，可以预见它将和coffeescript一样，成为javascript开发人员的标准备选，也许会有一些库和工具直接支持typescript，那样的话就会有更多人来使用typescript了。

如果你对typescript也感兴趣，欢迎加入QQ群：Typescript热情交流群（250299804）

## 相关工具还不够成熟

经过几天的试用，发现现在使用它也还是不够方便。主要有几下几点：

1.  Idea的编辑器支持typescript的高亮、格式化及代码提示，但还不能像java那样，实时显示错误，所以只能借助第三方工具（比如gruntjs）来编译并显示错误。这种方式对于一个静态类型的语言来说，不是很方便，因为类型方面的错误很容易犯，如果分成两步走，来回切换工具会让定位很麻烦。
2.  我使用的是gruntjs，它基于nodejs，有typescript插件可以编译typescript文件。但是由于nodejs在windows平台上的有一个bug导致无法全面取得错误信息，当ts文件有错误时，只能取到第一行（错误行号），而拿不到具体信息。有时候怎么看那一行都不知道错在哪儿，只好又另开一个cmd，调用tsc命令去编译，很麻烦。
3.  更麻烦的事情来了。在gruntjs中编译提示有误，但我手动调用tsc编译又没错，调试很久都不找不到原因。gruntjs就没法用了。
4.  Typescript不支持将多个ts文件合并生成一个js并压缩，所以我只能借助gruntjs工具实现，但gruntjs又有点问题。
5.  Typescript语言本身还存在一些问题。比如array明明有length属性，但不能被识别，导致明明与它匹配的接口会编译出错。另外，一个any[]类型的数组，应该可以包含各种对象，但放入一个string和一个函数后，提示有误，必须在string前加一个<any>（可能是因为Typescript目前还不支持泛型，导致推断有误）

综上所述，目前使用typescript进行前端开发，问题还比较多，各种小问题都会影响开发效率。所以看样子，只能老老实实地使用javascript（或者coffeescript?），等到typescript成熟一些后再用它。