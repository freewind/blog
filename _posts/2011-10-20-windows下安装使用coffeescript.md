title: windows下安装使用coffeescript
tags:
  - JavaScript
date: 2011-10-20 23:58:44
---

coffeescript是javascript的一种变种语言，它设计了各种好用的语法糖，让我们在写javascript代码时，会更加清晰简洁，带函数式语言的体验。

coffeescript有一个黄金定律：它就是Javascript

因为coffeescript提供了转换器，可以把写出来的coffee代码转换为等价的javascript代码。这样我们就可以在开发时写coffeescript，而在发布时把它转换为javascript。

例子可参看coffee的主页：[http://jashkenas.github.com/coffee-script/](http://jashkenas.github.com/coffee-script/)

有很多精彩的例子，看完后就会让人爱上写coffeescript。

这里讲的是如果在windows下安装coffeescript。

 <span id="more-450"></span>
<p>coffeescript依赖于node.js，它们这一套&#8221;运行于服务器端的js&#8221;通常在linux下会用的比较爽，因为node有一个叫npm(Node Package Manager)的工具，可以让我们方便地安装各种js库。比如在linux下安装coffee，只需要：

```
npm install -g coffee-script
```

可惜的是，这个npm工具不支持windows，如果想在windows下使用，还得安装一些如cygwin这样的linux模拟环境。如果我们仅仅想使用coffee的话，安装这个就太麻烦了。

在这个网页上，介绍了一种简单的方法：[https://github.com/alisey/CoffeeScript-Compiler-for-Windows](https://github.com/alisey/CoffeeScript-Compiler-for-Windows)

1.  在node.js网站上下载node.exe: [http://nodejs.org/#download](http://nodejs.org/#download)
2.  下载coffeescript: [http://github.com/jashkenas/coffee-script/tarball/master](http://github.com/jashkenas/coffee-script/tarball/master)
3.  然后创建一个coffee.cmd文件，内容如下：
```
@echo off
"C:/Node/node.exe" "C:/CoffeeScript/bin/coffee" %*
```

注意你需要把里面的路径写对，并把它加入到path中，以方便调用。

**测试**

创建一个文件，如test.coffee，内容如下：

```
square = (x) -> x * x
```

使用coffee.cmd将它编译为js文件：

```
coffee -c test.coffee
```

运行后，如果一切正常的话，你会发现test.coffee同级目录下，将会多出一个test.js文件，内容如下：

```
(function() {
  var square;

  square = function(x) {
    return x * x;
  };

}).call(this);
```

这样就说明我们的coffee安装成功了！

此命令行有以下参数可供使用

```
-c, --compile       compile to JavaScript and save as .js files
-o, --output [DIR]  set the directory for compiled JavaScript
-j, --join [FILE]   concatenate the scripts before compiling
-b, --bare          compile without the top-level function wrapper
-v, --version       display CoffeeScript version
-h, --help          display a list of available options
```