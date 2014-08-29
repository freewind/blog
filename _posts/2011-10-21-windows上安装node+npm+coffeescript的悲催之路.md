---
layout: post
title: windows上安装node+npm+coffeescript的悲催之路
tags: JavaScript
date: 2011-10-21 21:06:31
---

在linux上，安装它们都相当容易，步骤如下：

*   下载node(v0.4.12)的源代码，以标准方式安装(./configure & make & make install)
*   npm更简单：curl [http://npmjs.org/install.sh](http://npmjs.org/install.sh) | sh
*   coffeescript：npm install -g coffee-script

一切搞定，运行coffee就可以看到它的提示符，可以输入一些coffee代码试验。

但是在windows上，相当麻烦。首先，你不能使用cygwin来安装npm，见[http://npmjs.org/doc/README.html](http://npmjs.org/doc/README.html)

> Installing on Cygwin 
> Don't.
> 
> It's not supported, and terrible. Use the windows native approach, or use a Linux or Solaris virtual machine in VMWare or VirtualBox.

所以我们也不能使用cygwin来安装node了。（我是在花了一下午的时间在cygwin下安装好node后，才发现这一点的）

 <span id="more-452"></span>
<p>按&#8221;Installing on Windows&#8221;所写的方式，直接下载node.exe(v0.5.8)，再设置一堆属性，再安装npm，成功。

再安装coffeescript，使用`npm install -g coffee-script`安装成功，但是！

输入coffee后，进行coffee的提示符后，随便输入点什么，比如`a=2`，出错：

> coffee> a=2 
> Error: require.paths is removed. Use node_modules folders, or the NODE_PATH environment variable instead. 
> at Function.<anonymous> (module.js:360:11) 
> at Object.eval (D:\home\lib\node_modules\coffee-script\lib\coffee-script.js:97:32) 
> at Interface.<anonymous> (D:\home\lib\node_modules\coffee-script\lib\repl.js:44:34) 
> at Interface.emit (events.js:67:17) 
> at Interface._onLine (readline.js:153:10) 
> at Interface._line (readline.js:408:8) 
> at Interface._ttyWrite (readline.js:585:14) 
> at ReadStream.<anonymous> (readline.js:73:12) 
> at ReadStream.emit (events.js:70:17) 
> at onKeypress (tty_win32.js:46:10)

经过一番排查，发现原因是nodejs在0.5.x中改变了一些内部实现，导致coffeescript的代码无法正常工作。可是想安装nodejs 0.4.x的话，又没法安装npm。这可真让人纠结啊。

最终，还是按照[https://github.com/alisey/CoffeeScript-Compiler-for-Windows](https://github.com/alisey/CoffeeScript-Compiler-for-Windows)所讲的&#8221;Better solution&#8221;，直接使用node.exe与coffee结合在一起，去掉npm。再次进入coffee环境，输入代码，一切正常。
