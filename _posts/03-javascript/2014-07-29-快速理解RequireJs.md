---
layout: post
alias: understand-requirejs-quickly
tags: 未分类
date: 2014-07-29 00:49:48
title: 快速理解RequireJs
---

RequireJs已经流行很久了，我们在项目中也打算使用它。它提供了以下功能：

1. 声明不同js文件之间的依赖
2. 可以按需、并行、延时载入js库
3. 可以让我们的代码以模块化的方式组织

初看起来并不复杂。

## 在html中引入requirejs

在HTML中，添加这样的`<script>`标签：

    <script src="/path/to/require.js" data-main="/path/to/app/config.js"></script>

通常使用requirejs的话，我们只需要导入requirejs即可，不需要显式导入其它的js库，因为这个工作会交给requirejs来做。

属性`data-main`是告诉requirejs：你下载完以后，马上去载入真正的入口文件。它一般用来对requirejs进行配置，并且载入真正的程序模块。

## 在config.js中配置requirejs

`config.js`中通常用来做两件事：

1. 配置requirejs 比如项目中用到哪些模块，文件路径是什么
2. 载入程序主模块

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    app: 'app'
  }
});

requirejs(['app'], function(app) {
  app.hello();
});
```

在`paths`中，我们声明了一个名为`app`的模块，以及它对应的js文件地址。在最理想的情况下，`app.js`的内容，应该使用requirejs的方式来定义模块：

```js
define([], function() {
  return {
    hello: function() {
      alert("hello, app~");
    }
  }
});
```

这里的`define`是requirejs提供的函数。requirejs一共提供了两个全局变量：

1. requirejs/require: 用来配置requirejs及载入入口模块。如果其中一个命名被其它库使用了，我们可以用另一个
1. define: 定义一个模块

另外还可以把`require`当作依赖的模块，然后调用它的方法：

```js
define(["require"], function(require) {
    var cssUrl = require.toUrl("./style.css");
});
```

## 依赖一个不使用requirejs方式的库

前面的代码是理想的情况，即依赖的js文件，里面用了`define(...)`这样的方式来组织代码的。如果没用这种方式，会出现什么情况？

比如这个`hello.js`:

```js
function hello() {
  alert("hello, world~");
}
```

它就按最普通的方式定义了一个函数，我们能在requirejs里使用它吗？

先看下面不能正确工作的代码：

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    hello: 'hello'
  }
});

requirejs(['hello'], function(hello) {
  hello();
});
```

这段代码会报错，提示：

    Uncaught TypeError: undefined is not a function 

原因是最后调用`hello()`的时候，这个`hello`是个`undefined`. 这说明，虽然我们依赖了一个js库（它会被载入），但requirejs无法从中拿到代表它的对象注入进来供我们使用。

在这种情况下，我们要使用`shim`，将某个依赖中的某个全局变量暴露给requirejs，当作这个模块本身的引用。

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    hello: 'hello'
  },
  shim: {
    hello: { exports: 'hello' }
  }
});

requirejs(['hello'], function(hello) {
  hello();
});
```

再运行就正常了。

上面代码`exports: 'hello'`中的`hello`，是我们在`hello.js`中定义的`hello`函数。当我们使用`function hello() {}`的方式定义一个函数的时候，它就是全局可用的。如果我们选择了把它`export`给requirejs，那当我们的代码依赖于`hello`模块的时候，就可以拿到这个`hello`函数的引用了。

所以：`exports`可以把某个非requirejs方式的代码中的某一个全局变量暴露出去，当作该模块以引用。

### 暴露多个变量：init

但如果我要同时暴露多个全局变量呢？比如，`hello.js`的定义其实是这样的：

```js
function hello() {
  alert("hello, world~");
}
function hello2() {
  alert("hello, world, again~");
}
```

它定义了两个函数，而我两个都想要。

这时就不能再用`exports`了，必须换成`init`函数：

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    hello: 'hello'
  },
  shim: {
    hello: {
      init: function() {
        return {
          hello: hello,
          hello2: hello2
        }
      }
    }
  }
});

requirejs(['hello'], function(hello) {
  hello.hello1();
  hello.hello2();
});
```

当`exports`与`init`同时存在的时候，`exports`将被忽略。

## 无主的与有主的模块

我遇到了一个折腾我不少时间的问题：为什么我只能使用`jquery`来依赖jquery, 而不能用其它的名字？

比如下面这段代码：

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    myjquery: 'lib/jquery/jquery'
  }
});

requirejs(['myjquery'], function(jq) {
  alert(jq);
});
```

它会提示我：

    jq is undefined

但我仅仅改个名字：

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    jquery: 'lib/jquery/jquery'
  }
});

requirejs(['jquery'], function(jq) {
  alert(jq);
});
```

就一切正常了，能打印出`jq`相应的对象了。

为什么？我始终没搞清楚问题在哪儿。

### 有主的模块

经常研究，发现原来在jquery中已经定义了：

    define('jquery', [], function() { ... });

它这里的`define`跟我们前面看到的`app.js`不同，在于它多了第一个参数`'jquery'`，表示给当前这个模块起了名字`jquery`，它已经是有主的了，只能属于`jquery`.

所以当我们使用另一个名字：

    myjquery: 'lib/jquery/jquery'

去引用这个库的时候，它会发现，在`jquery.js`里声明的模块名`jquery`与我自己使用的模块名`myjquery`不能，便不会把它赋给`myjquery`，所以`myjquery`的值是`undefined`。

所以我们在使用一个第三方的时候，一定要注意它是否声明了一个确定的模块名。

### 无主的模块

如果我们不指明模块名，就像这样：

```js
define([...], function() {
  ...
});
```

那么它就是无主的模块。我们可以在`requirejs.config`里，使用任意一个模块名来引用它。这样的话，就让我们的命名非常自由，大部分的模块就是无主的。

### 为什么有的有主，有的无主

可以看到，无主的模块使用起来非常自由，为什么某些库（jquery, underscore）要把自己声明为有主的呢？

按某些说法，这么做是出于性能的考虑。因为像`jquery`, `underscore`这样的基础库，经常被其它的库依赖。如果声明为无主的，那么其它的库很可能起不同的模块名，这样当我们使用它们时，就可能会多次载入jquery/underscore。

而把它们声明为有主的，那么所有的模块只能使用同一个名字引用它们，这样系统就只会载入它们一次。

### 挖墙角

对于有主的模块，我们还有一种方式可以挖墙角：不把它们当作满足requirejs规范的模块，而当作普通js库，然后在`shim`中导出它们定义的全局变量。

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    myjquery: 'lib/jquery/jquery'
  },
  shim: {
    myjquery: { exports: 'jQuery' }
  }
});

requirejs(['myjquery'], function(jq) {
  alert(jq);
});
```

这样通过暴露`jQuery`这个全局变量给`myjquery`，我们就能正常的使用它了。

不过我们完全没有必要这么挖墙角，因为对于我们来说，似乎没有任何好处。

## 如何完全不让jquery污染全局的$

在前面引用jquery的这几种方式中，我们虽然可以以模块的方式拿到jquery模块的引用，但是还是可以在任何地方使用全局变量`jQuery`和`$`。有没有办法让jquery完全不污染这两个变量？

### 在init中调用noConflict (无效)

首先尝试一种最简单但是不工作的方式：

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    jquery: 'lib/jquery/jquery'
  },
  shim: {
    jquery: {
      init: function() {
        return jQuery.noConflict(true);
      }
    }
  }
});

requirejs(['jquery'], function(jq) {
  alert($);
});
```

这样是不工作的，还是会弹出来一个非`undefined`的值。其原因是，一旦requirejs为模块名`jquery`找到了属于它的模块，它就会忽略`shim`中相应的内容。也就是说，下面这段代码完全没有执行：

```
jquery: {
  init: function() {
    return jQuery.noConflict(true);
  }
}
```

### 使用另一个名字

如果我们使用挖墙角的方式来使用jquery，如下：

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    myjquery: 'lib/jquery/jquery'
  },
  shim: {
    myjquery: {
      init: function() {
        return jQuery.noConflict(true);
      }
    }
  }
});

requirejs(['myjquery'], function(jq) {
  alert($);
});
```

这样的确有效，这时弹出来的就是一个`undefined`。但是这样做的问题是，如果我们引用的某个第三方库还是使用`jquery`来引用jquery，那么就会报“找不到模块”的错了。

我们要么得手动修改第三方模块的代码，要么再为它们提供一个`jquery`模块。但是使用后者的话，全局变量`$`可能又重新被污染了。

### 使用map

如果我们有办法能让在继续使用`jquery`这个模块名的同时，有机会调用`jQuery.noConflict(true)`就好了。

我们可以再定义一个模块，仅仅为了执行这句代码：

**jquery-private.js**

```js
define(['jquery'], function(jq) {
  return jQuery.noConflict(true);
});
```

然后在入口处先调用它:

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    jquery: 'lib/jquery/jquery',
    'jquery-private': 'jquery-private'
  }
});

requirejs(['jquery-private', 'jquery'], function() {
  alert($);
});
```

这样的确可行，但是还是会有问题： 我们必须小心的确保`jquery-private`永远是第一个被依赖，这样它才有机会尽早调用`jQuery.noConflict(true)`清除全局变量`$`和`jQuery`。这种保证只能靠人，非常不可靠。

我们这时可以引入`map`配置，一劳永逸地解决这样问题：

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    jquery: 'lib/jquery/jquery',
    'jquery-private': 'jquery-private'
  },
  map: {
    '*': { 'jquery': 'jquery-private'},
    'jquery-private': { 'jquery': 'jquery'}
  }
});

requirejs(['jquery'], function(jq) {
  alert($);
});
```

这样做，就解决了前面的问题：在除了jquery-private之外的任何依赖中，还可以直接使用`jqurey`这个模块名，并且总是被替换为对`jquery-private`的依赖，使得它最先被执行。
