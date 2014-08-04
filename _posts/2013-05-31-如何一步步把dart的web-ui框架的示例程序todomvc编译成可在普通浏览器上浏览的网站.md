---
layout: post
title: 如何一步步把dart的web-ui框架的示例程序todomvc编译成可在普通浏览器上浏览的网站
tags:
  - 其它语言
date: 2013-05-31 00:16:12
---

Dart的web-ui框架看起来似乎比较成熟了，看了一下它的示例代码，觉得很清晰，web-component的方式比较新颖，再加上可以用dart来写，隐隐有超越angularjs之势，所以想把它的示例跑起来，看看浏览器兼容性及生成代码大小有没有什么问题，是否可以用它来写点什么东西。

首先看效果：http://freewind.me/test/dart/web-ui-todomvc/

源代码来自web-ui官方提供的示例代码，我做的只是在IDE下试了看对dart的支持程度，以及调用相关命令生成最终的js版，技术含量不高，不过也遇到了不少坑。应老猪要求，把过程记录下来，他可能想自己动手试试。

先分析一下这个程序，看看它有什么特点。

1.  浏览器兼容性

在chrome/firefox/IE10，以及安卓4.0上的浏览器中成功运行。安卓4.0上的QQ浏览器没有任何问题，而自带浏览器有时候会正常显示某些项，可能是css的问题。在IE8下无法运行。看官网上说支持多数浏览器，IE系只支持9和10，看来不太适合做面向普通中国大众的网站。

2.  文件大小

index.html 829B

     index.html.css 2.14K

     base.css 5.81K

     dart.js  8.27K

     index.html_bootstrap.dart.js 276.63K

这里最后那个js文件是最重要的，它已经经过minify操作，原始大小好像是500多K。它是由dart代码转换过来的，文件名里的bootstrap跟twitter的bootstrap没有关系。

dart.js是一个引导文件，不论我们是用web-ui，还是用dart提供的最基本的dart:html库，都会（且必须）用到它。好在它个头不大。

index.html很小，里面基本上没有什么内容。原来web-ui会把所有html页面都转换dart代码，还会变态地把某些html代码嵌入到dart里面，最后生成了一个奇大无比的js文件。不过，虽然这个看着有点大，想到它已经包含了dart的js、web-ui的js、html代码以我们手写的dart，其实已经不大了。再经过gzip一样，这个尺寸就算对于一个互联网应用来说也可接受。（你不需要再包含各种js库如jquery、underscore什么的）

由于它把html代码放到了js里，并且还可以minify，这个应用对于搜索引擎来说是基本上不友好的。而且它默认通过url上的hash的变化来局部切换页面，所以只能用来做单页应用，看来它的设计定位还是相当明确的。如果需要做SEO，可能还得专门做一些静态页面供搜索引擎使用。

关于语言特性和框架特色这里就不多讲，虽然有很多激动人心的地方。这里只讲如何从零开始一步步把它跑起来。

我们需要这几个东西：dart-sdk（包括dart命令行、dart2js转换器、pub包管理程序等），dartium(嵌入了dartvm的浏览器），web-ui相关的库，web-ui提供的todomvc源代码，IDEA的dart插件。

1.  dart-sdk

在dart官网http://dartlang.org上，它默认提供了dart editor的下载。它是一个基于eclipse的dart编辑器，里面还包含了dart-sdk, dartium等。你可以直接下载它就全齐了，或者，像我一样不想用eclipse，那就分开下载。

    首先是http://www.dartlang.org/tools/sdk/上看到sdk的下载链接，不过不要点那个大按钮下stable版，而要在下面找未经测试的流血受伤版。因为web-ui要求的dartsdk版本比较高，stable版的没法编译。我下载的是latest的mac-64。

    下载后解压到某地，然后把其中的bin目录加到PATH中，运行dart、dart2js、pub等命令看看是否安装成功了。

2.  IDEA插件

安装两个插件，一个Dart，一个File Watcher。前者不用多说，提供了对Dart和web-ui的支持，后者可以监视目录中的dart文件，发现修改时自动编译为js，比较方便。不过如果我们使用Dartium来开发，实际上用到它的机会不多，因为dartium可直接运行dart代码，修改后一刷新即可，省了编译的时间，非常方便。

    安装后，要记得配置dart sdk路径，不然你会发现虽然可以识别dart语法，但不能编译、检查和提示方法。我在这里被坑了一下，还以为是dart的插件太弱了呢。

    有几个工具和命令需要注意一下。工具栏里有个dart的图标，可以很方便的设置dart sdk。点文件右键菜单里有run，可以执行一个dart文件（当然不能引用dart:html等前端库，还要有main()方法）。右键里还有一个dart2js，可以把一个dart文件转换为js，并且提供了checked和minify两个选项，很方便。其中checked是指在生成的js里添加上类型方面的检查方便调试，minify是对源代码进行处理变小一些。一般情况下会只勾选minify，以得到最小的js文件。

3.  Dartium

它是一个跟chrome很像的浏览器，基于同一个内核，不同的是它内嵌了dartvm，所以我们可以直接用它来执行dart代码，大大方便了开发，实现改完就刷的效果。由于dartsdk更新很快，所以注意要使用最新的dartium，经常更换，否则可能出现IDE与浏览器打架的情况。

    安装过程比较简单，到http://www.dartlang.org/tools/dartium/下载后，解压即可。这里又有一个坑需要注意：你可以用它直接浏览引用了dart:html的库的应用，但不能正常运行web-ui的代码，虽然看起来页面里也是dart代码。这是因为，web-ui写出来的程序，还必须经常dwc这个编译工具的处理，才能转换为可正常执行的dart代码。

    我在这里卡了不少时间，因为我写了一个web-ui的hello world，但怎么都没法正常运行，还以为我装错了或没设置好，装了几遍。

4.  pub

pub是dart自带的包管理程序。一般需要在项目目录下提供一个pubspec.yaml的文件，里面写上一些内容，比如项目名，依赖项等，示例如下（web-ui提供的）：

    name: web_ui
author: "Web UI Authors <web-ui-dev@dartlang.org>"
homepage: https://github.com/dart-lang/web-ui
description: An easy way to build web apps in Dart.
version: 0.4.9
dependencies:
  args: ">=0.4.5"
  analyzer_experimental: ">=0.5.12"
  browser: ">=0.4.5"
  logging: ">=0.4.5"
  pathos: ">=0.4.5"
  unittest: ">=0.4.5"
  # Pin these github packages so we don't get breaks from publishing them:
  csslib: ">=0.4.7 <0.4.8"
  html5lib: ">=0.4.3 <0.4.4"
  source_maps: ">=0.4.5"
dev_dependencies:
  js: any
  benchmark_harness: ">=1.0.2 <1.0.3"
environment:
  sdk: ">=0.5.12"

```

然后在命令行执行：

     pub install

```

它就会自动寻找各依赖，下载后放在packages目录下。具体详情可参看其官网：http://pub.dartlang.org/

5.  最后是web-ui

先到项目主页https://github.com/dart-lang/web-ui上把它clone下来：

    git clone git://github.com/dart-lang/web-ui.git

```

它里面包含了所有的源代码以及example目录，其下有todomvc这个例子。

    首先在web-ui目录下执行`pub install`，如果dart sdk版本不对，它会提示。成功的话会看到它下载了不少依赖。

    然后进行example下的todomvc目录，执行：

    ./build.dart

```

它实际上是通过代码方式，调用了web-ui的编译器，根据html文件生成了一堆dart文件。生成的文件会放在out目录下，这是写死的，只能是out这个目录名。

    进入out目录，可以看到里面多了很多文件。我们可以用dartium浏览器直接打开其中的index.html，正常情况下会看到todolist的经典界面，也可以经行操作。但此时用其它浏览器打开它，是没法正常运行的。

    要想让各浏览器正常运行，还需要一条命令。在out目录下：

    dart2js index.html_bootstrap.dart -oindex.html_bootstrap.dart.js

```

它会把最主要的那个dart文件转成js文件，只需要转这一个即可，其它的dart实际上被包含到里面去了。我们不需要对index.html进行修改，就可以使用普通的浏览器查看了，似乎里面有自动判断是否有js版的功能。

    另外，通过dart2js转换的，没有minify选项。所以我还是到IDEA中用它的dart2js菜单来做。

    这里总结一下，web-ui写出来的代码是不能直接运行的，它必须首先用build.dart编译一次，生成可正常运行的dart文件，才可以在dartium上查看。然后还要执行dart2js的编译，才能在普通的浏览器上执行。这意味着，我们修改文件后，还是需要至少编译一次，才能看到效果。看来需要试试file watcher，能不能增加一个文件变化时自动调用build.dart的功能。

    看到这里 https://groups.google.com/a/dartlang.org/forum/?fromgroups#!topic/misc/vqdCPt-OKB8 说，dartium有一个web-ui的插件，可以让我们不用编译就可以直接在dartium查看，实现即改即刷。刚安装试了一下没成功，如果能行的话就方便了。

6.  上传到网上

这时你可以把out目录下的文件上传到网上，供人们浏览。它会引用到packages目录下的一些东西，而packages可能是一个目录的软链接，所以需要手动在上传目录中建立packages目录，并根据浏览器的提示把缺少的文件补上去。

安装过程结束，有问题到群里问我。等我好好研究web-ui，做点练手的程序之后，再写一些关于它的心得。