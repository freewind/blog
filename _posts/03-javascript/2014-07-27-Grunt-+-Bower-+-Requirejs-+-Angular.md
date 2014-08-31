---
layout: post
id: 2739
alias: grunt-bower-requirejs-angular
tags: 未分类
date: 2014-07-27 22:08:46
title: Grunt + Bower + Requirejs + Angular
---

现在web开发的趋势是前后端分离。前端采用某些js框架，后端采用某些语言提供restful API，两者以json格式进行数据交互。

如果后端采用node.js，则前后端可以使用同一种语言，共享某些可重用的Js代码，并共享构建工具。但很多时候我们可能采用别的语言，如ruby/java/scala等，此时前后端代码基本上是完全独立的。虽然大家都在同一个项目中，但可以分成互相独立的两块，并且前后端通常使用不同的构建工具。

比如当后端使用Scala时，我们会使用sbt进行项目构建，对scala代码进行编译、测试、打包等。它的专长是处理与scala相关的任务，但对于前端的支持比较弱。前端一些常见的任务，如js库的下载和管理、css文件的转换、js文件合并压缩、js测试的执行等，很难在sbt中找到好用的插件，而利用js世界里的工具来做反而更加方便一些。

我们项目组这几天正在讨论是否在项目中引入一些前端框架，还是直接用原生Javascript写。经过反复讨论和调研，最终决定引入AngularJs。但AngularJs的引入并不是单一的任务，因为我们还需要考虑前端代码的测试、依赖管理等，都需要有相应的工具支持，所以最后引入了这么一整套工具：

1. Grunt - Js任务管理工具，通过各种插件对项目进行各种操作，比如文件转换、运行测试、打包部署等。相当于java里的ant/maven/gradle，ruby中的rack，scala中的sbt。
1. Bower - Js库依赖管理工具。当你需要jquery时，不需要手动下载，只需要执行`bower install jquery`
1. RequireJs - Js库加载管理，及模块化支持。可以按需及并行加载js库，可以把我们的代码以模块化的方式组织。
1. AngularJs - Js前端框架，支持依赖注入，双向绑定等我认为很重要的功能

这套东西都是比较基础且使用比较广泛的。一般一旦在项目中引入前端框架，或者需要写比较多的Js代码时，我们都会采用它们，所以很有必要学习并掌握它们。

## 各工具都相当的独立

在开始前，需要先提示一下，在javascript的世界里，很多东西都是由社区提供的，所以每一种工具都相当独立。比如，很多工具都有着自己独立的配置文件，自己的命令行参数，有时候还需要有一些额外的插件把两个工具结合起来。

所以下面将会有很多比较琐碎的命令，我们需要一一了解。不过好在我们一旦了解了，下次就可以使用已经配置好的文件，通过几条命令将把有的东西都准备好，很方便。

## 安装nodejs

在Mac中，我们可以使用brew来安装。在其它系统下，请使用相应的工具或直接到官网下载。

    brew install node

Nodejs可以让我们在服务器端使用javascript编程，它是很多js工具的基础。如果你已经安装，请确保使用最新版本：

    brew upgrade node

查看版本：

    node -v

我这里显示：

    v0.10.28

## npm

Npm是node官方提供的包依赖管理工具。我们下面使用的grunt等，都是以插件形式下载安装的。

当我们安装好nodejs后，`npm`就自动可用了。

查看版本：

    npm -v

我这里显示：

    2.0.0-alpha-5

## 创建项目目录

下面我们从零开始，首先在任意位置新建一个目录作为我们的项目根目录，比如：

    mkdir ~/myproject

然后进入该目录：

    cd ~/myproject

后面的命令都将在项目根目录下操作。

## 为npm创建package.json

首先我们需要为npm提供一个`package.json`，告诉它我们的项目信息，特别是项目中将会使用的插件。我们不需要手动创建，因为可以直接调用以下命令：

    npm init

它会问我们一些问题，我们可以按需回答，也可以全部使用默认值，反正以后可以改起来也很容易。

最后将会产生如下的`package.json`文件：

```json
{
  "name": "grunt-bower-angular-demo",
  "version": "0.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/freewind/grunt-bower-angular-demo.git"
  },
  "author": "",
  "license": "BSD",
  "bugs": {
    "url": "https://github.com/freewind/grunt-bower-angular-demo/issues"
  }
}
```

对于像我们这样的非nodejs项目来说，里面的大部分内容都没用，可以删掉大部分，只剩下：

```json
{
  "name": "grunt-bower-angular-demo",
  "version": "0.0.0"
}
```

## 安装 grunt

    npm install grunt --save-dev

将使用npm下载grunt插件，它们将保存到项目根目录下的`node_components`目录下。

后面的`--save-dev`参数是说，把这个插件信息，同时添加到`package.json`的`devDependencies`中：

```json
"devDependencies": {
  "grunt": "~0.4.5"
}
```

由于grunt仅在开发阶段使用，所以使用`--save-dev`。如果是运行时使用的，则用`--save`

## 安装 grunt-cli

上面安装的`grunt`并不包含命令行工具，我们还需安装相应的`grunt-cli`，才能在命令行中调用`grunt`命令：

    npm install grunt-cli -g

后面的`-g`是说，把`grunt-cli`安装成全局工具，以便在任意目录下使用。

安装后，输入:

    grunt --version

我这里显示为：

    grunt-cli v0.1.13
    grunt v0.4.5

在比较少的情况下，可能提示找不到grunt，则需要根据安装grunt-cli时的提示信息，把相应的路径添加到`PATH`中：

    echo PATH=$PATH:/your/path/to/grunt >> ~/.bashrc
    source ~/.bashrc


## 为grunt创建配置文件Gruntfile.js

### 安装grunt-init

    npm install grunt-init -g

### 下载grunt模板

    git clone https://github.com/gruntjs/grunt-init-gruntfile.git ~/.grunt-init/gruntfile

### 生成Gruntfile 

    grunt-init gruntfile

根据需要回答问题，或者使用默认值，将得到以下`Gruntfile.js`文件：

```js
/*global module:false*/
module.exports = function(grunt) {

  // Project configuration.
  grunt.initConfig({
    // Metadata.
    pkg: grunt.file.readJSON('package.json'),
    banner: '/*! <%= pkg.title || pkg.name %> - v<%= pkg.version %> - ' +
      '<%= grunt.template.today("yyyy-mm-dd") %>\n' +
      '<%= pkg.homepage ? "* " + pkg.homepage + "\\n" : "" %>' +
      '* Copyright (c) <%= grunt.template.today("yyyy") %> <%= pkg.author.name %>;' +
      ' Licensed <%= _.pluck(pkg.licenses, "type").join(", ") %> */\n',
    // Task configuration.
    concat: {
      options: {
        banner: '<%= banner %>',
        stripBanners: true
      },
      dist: {
        src: ['lib/<%= pkg.name %>.js'],
        dest: 'dist/<%= pkg.name %>.js'
      }
    },
    uglify: {
      options: {
        banner: '<%= banner %>'
      },
      dist: {
        src: '<%= concat.dist.dest %>',
        dest: 'dist/<%= pkg.name %>.min.js'
      }
    },
    jshint: {
      options: {
        curly: true,
        eqeqeq: true,
        immed: true,
        latedef: true,
        newcap: true,
        noarg: true,
        sub: true,
        undef: true,
        unused: true,
        boss: true,
        eqnull: true,
        browser: true,
        globals: {
          jQuery: true
        }
      },
      gruntfile: {
        src: 'Gruntfile.js'
      },
      lib_test: {
        src: ['lib/**/*.js', 'test/**/*.js']
      }
    },
    qunit: {
      files: ['test/**/*.html']
    },
    watch: {
      gruntfile: {
        files: '<%= jshint.gruntfile.src %>',
        tasks: ['jshint:gruntfile']
      },
      lib_test: {
        files: '<%= jshint.lib_test.src %>',
        tasks: ['jshint:lib_test', 'qunit']
      }
    }
  });

  // These plugins provide necessary tasks.
  grunt.loadNpmTasks('grunt-contrib-concat');
  grunt.loadNpmTasks('grunt-contrib-uglify');
  grunt.loadNpmTasks('grunt-contrib-qunit');
  grunt.loadNpmTasks('grunt-contrib-jshint');
  grunt.loadNpmTasks('grunt-contrib-watch');

  // Default task.
  grunt.registerTask('default', ['jshint', 'qunit', 'concat', 'uglify']);

};
```

它里面已经包含了一些常用的插件，比如`grunt-contrib-jshint`等，我们可根据需要删减一些用不上的。

它同时还会在`package.json`里添加上这些插件的依赖：

```json
"grunt-contrib-concat": "~0.4.0",
"grunt-contrib-jshint": "~0.10.0",
"grunt-contrib-qunit": "~0.5.2",
"grunt-contrib-uglify": "~0.5.0",
"grunt-contrib-watch": "~0.6.1"
```

这些插件还未下载，如果需要，可以运行:

    npm install

把它们下载到本地

## bower

    npm install bower -g

package.json

```json
{
  "engines": {
    "node": ">= 0.10.0"
  },
  "devDependencies": {
    "grunt": "~0.4.5",
    "grunt-contrib-jshint": "~0.10.0",
    "grunt-contrib-watch": "~0.6.1",
    "grunt-contrib-qunit": "~0.5.2",
    "grunt-contrib-concat": "~0.4.0",
    "grunt-contrib-uglify": "~0.5.0"
  }
}
```

## 安装bower

bower跟npm在某种意义上相似，它是用来管理常用的js库的依赖的，比如jquery, underscore, angularjs等。

我们可以通过npm安装它：

    npm install bower -g

把它装为全局工具

## 为bower生成配置文件bower.json

bower也有它自己的配置文件`bower.json`，我们不需要手动创建。

    bower init

将会生成如下的`bower.json`:

```json
{
  "name": "grunt-bower-angular-demo",
  "version": "0.0.0",
  "homepage": "https://github.com/freewind/grunt-bower-angular-demo",
  "authors": [
    "Peng Li <nowind_lee@qq.com>"
  ],
  "license": "MIT",
  "ignore": [
    "**/.*",
    "node_modules",
    "bower_components",
    "test",
    "tests"
  ]
}
```

对于我们的项目来说，里面的东西基本上都没用。有用的是后面将会出现的`dependencies`

## 下载requirejs、jquery、angularjs

    bower insall requirejs --save
    bower insall jquery --save
    bower install angularjs --save

将会自动下载jquery到angularjs相应的文件，到项目根目录下的`bower_components`目录。并在`bower.json`中添加：

```json
"dependencies": {
  "angularjs": "~1.2.20",
  "jquery": "~2.1.1",
  "requirejs": "~2.1.14"
}
```

## 安装grunt-bower-task

bower只负责把依赖下载到本地的`bower_components`目录，并不负责把它们拷贝到我们项目中实际使用的地方，比如`public/js/lib`目录下。

为了实现这样的功能，我们还需要另一个插件的帮助：

    npm install grunt-bower-task --save-dev

然后打开其文档：https://www.npmjs.org/package/grunt-bower-task ，按照上面的提示进行配置。

首先在`Gruntfile`中合适位置添加：

    grunt.loadNpmTasks('grunt-bower-task');

然后在`grunt.initConfig({...})`参数中，添加相应的配置项：

```json
bower: {
    install: {
      options: {
        targetDir: './public/js/lib',
        layout: 'byComponent',
        install: true,
        verbose: false,
        cleanTargetDir: false,
        cleanBowerDir: false,
        bowerOptions: {}
      }
    }
  }
```

这里指定拷贝的目标目录为`public/js/lib`，且文件按照模块分成单个目录(byComponent)。如果想把所有的js放在同一个目录，所有的css文件放在同一个目录，则使用`byType`

## 关于RequireJs

在前面我们已经使用bower安装了requirejs:

    bower install requirejs

RequireJs可用来管理页面中使用的js库之间的依赖关系，可以按需、并行、延迟加载js库。同时它可以让我们以模块化的形式组织js代码。

强烈建议先了解RequireJs的运行原理再动手写代码，不然肯定会遇到各种坑。RequireJs的文档写得有点绕，可参考我的另一篇文章：TODO

## 配置RequireJs

前面我们第三方的依赖，通过grunt-bower-task拷贝到了`public/js/lib`目录下。我们自己写的js，将会放置在`public/js`目录下。

我们需要手动创建一个`config.js`文件，用来配置和初始化requirejs。

### 在HTML中引入requirejs

如果我们使用了requirejs，则在HTML中，我们通常只需要一个`<script src="..."></script>`标签引入requirejs并指定入口文件即可，而不需要写多个`script`标签手动加载其它js文件。

在HTML中合适位置加入：

    <script src="/public/js/lib/requirejs/require.js" data-main="/public/js/config.js"></script> 

这里首先加载了require.js，并通过`data-main`属性告诉requirejs：当你加载完以后，请加载config.js文件进行初始化。

### config.js

`config.js`内容如下：

```js
requirejs.config({
  baseUrl: '/public/js',
  paths: {
    app: 'app',
    jquery: 'lib/jquery/jquery',
    angular: 'lib/angularjs/angular'
  },
  shim: {
  }
});

requirejs(['app'], function(app) {
  app.hello();
});
```

我们在`paths`中声明了几个模块，其中的`app`是我们自己创建的js文件，用于放我们自己的业务代码，它对应于`/public/js/app.js`. `jquery`与`angular`对应的文件是我们使用grunt-bower-task拷贝过来的第三方js库。

`shim`中用来处理一些没有遵守requirejs规范的js库，比如`underscore`之类。可在里面对它们进行一些依赖声明、初始化操作等。这里暂时用不上。

最后用`requirejs`来导入我们自己的模块，可在后面的callback中拿到对应模块的实例，并对它进行一些操作，比如我们调用了`app.hello()`方法。

### app.js

为了让这个例子完整，我们可以定义相应的`app.js`:

```js
define([], function() {
  return {
    hello: function() {
      alert("hello, requirejs");
    }
  }
});
```

### index.html

为了能让例子跑起来，我们还需要创建一个`public/index.html`，内容如下：

```html
<html>
<head>
  <script src="/public/js/lib/requirejs/require.js" 
          data-main="/public/js/config.js"></script>
</head>
<body>
  <div>Hello, world!</div>
</body>
</html>
```

### 启动 web server

进入项目根目录，运行：

    grunt bower

把bower下载的js库拷贝到`public/js/lib`下。

然后启动web server:

    python -m SimpleHTTPServer

最后打开浏览器，访问：

    http://localhost:8000/public/index.html

如果一切正常的话，会看到弹出一个提示框，上面内容为：

    hello, requirejs


## Angularjs

然后我们做一个简单的angular的例子。

由于angularjs并不是按requirejs的模块方式组织代码的，我们需要在`config.js`中添加：

```json
shim: {
    angular : { exports : 'angular'}
}
```

Angularjs会在全局域中添加一个名为`angular`的变量。我们必须在`shim`中显式把它暴露出来，才能通过模块注入的方式使用它，比如：

```js
define(['angular'], function(ng) {
  // we can use argument `ng` instead of gloabl `angular` now
});
```

### 在index.html中添加angular代码

```html
<div ng-controller="MyController">
  <input type="text" ng-model="name" />
  <span>{{name}}</span>
</div>
```

### 准备相应的controller

把`app.js`的内容改为：

```js
define(['angular'], function(angular) {
  angular.module('myApp', [])
      .controller('MyController', ['$scope', function ($scope) {
        $scope.name = 'Change the name';
      }]);

    angular.element(document).ready(function() {
      angular.bootstrap(document, ['myApp']);
    });
});
```

在这段代码里，我定义了一个angularjs自己的模块`myApp`，以及相应的`MyController`。在后面，通过`angular.bootstrap`方法，把该模块与`document`结合在了一起。

启动web server，就可以看到效果了。当我们修改了页面上输入框里的内容，它旁边的文字也会跟着改变。

## 修改angularjs的占位符

在html中显示angularjs里的一个字段时，我们使用`{{}}`来占位，比如：

  <span>{{name}}</span>

如果我们同时使用了mustcahe模板，就会有冲突。我们可以更改angularjs的配置：

```js
angular.module('myApp', []).config(function($interpolateProvider){
        $interpolateProvider.startSymbol('[[').endSymbol(']]');
    }
);
```

然后我们就可以写成：

  <span>[[name]]</span>

了.

## 项目代码

上面的操作，都在这个项目中： <https://github.com/freewind/grunt-bower-angular-demo>

另外，关于requirejs/angularjs的结合使用，可以参看这个比较好的样板项目： <https://github.com/tnajdek/angular-requirejs-seed>
