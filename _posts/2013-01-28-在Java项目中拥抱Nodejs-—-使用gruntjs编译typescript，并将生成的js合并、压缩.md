---
layout: post
title: 在Java项目中拥抱Nodejs — 使用gruntjs编译typescript，并将生成的js合并、压缩
tags: JavaScript
date: 2013-01-28 00:18:11
---

作为一个Java程序员，做的是Java项目，为什么需要了解Nodejs，并且还要拥抱它？

这里的Java项目，特指java web项目。如果是纯java项目，并不是很需要它，但如果是有很js/css/coffeescript/typescript/less/sass这样的文件的项目时，也许你就需要Nodejs了。

Nodejs是一个平台，可以让我们写服务器端的代码，它内部使用了google开发的v8引擎，执行js代码速度很快。我们学习它并不是为了写js代码，而是为了把它当工具使用，更好地处理我们的js/css等文件。

Nodejs的启动速度很快，并且第三方模块很多，而这些模块很多都跟js相关。其中有一些很好用的工具，可以简化我们的工作。虽然学习它需要花一些时间，但是带来的回报更大。

这里我将使用它来管理我的js代码，要求实现以下功能：

1.  将我的typescript编译为js代码2.  将多个js文件合并为一个3.  将合并后的js文件进行精简操作(min)，减少文件体积4.  当我修改了typescript/js代码，自动重复以上操作

在Playframework中，可以内置或通过插件，将coffeescript自动转为javascript并精简压缩。但是当我们需要更多更灵活的功能时，就比较麻烦，因为play的插件不是那么好写的。我今天在play1中，编写java代码来处理typescript的编译操作，但是发现效率很低，当文件多的时候，修改typescript文件刷新后，需要好几秒才能编译成新的，难以接受。另外，由于typescript现在不支持将多个ts文件合并为一个js文件，我想去合并、精简时生成的多个js文件时，觉得太麻烦没做，只好将就着。

后来实在受不了了，发现了gruntjs这个工具，它是基于nodejs的。原来用它实现这些任务竟然如此简单，只需要简单的配置即可，编译速度还很快。另外，除了这些任务，我还发现了nodejs上的livereload插件，这样就可以替代以前使用的那个python的livereload了。

这些经验不仅仅对现在的项目有用，以后做其它涉及到js的项目时，也同样可用。所以学习它很值得。

## Nodejs

Nodejs现在发展势头良好，正在紧张开发之中，每隔几天就看到一个新版本，让人眼花缭乱，当前最新版是v0.8.18。它的官方地址是[http://nodejs.org/](http://nodejs.org/)，可以在windows/linux/unix上安装。

打开主页后，网站会根据操作系统自动提供相应的安装包，下载后安装即可。我是win7 x64，安装完成后，在cmd中输入：

    node -v
    

    会提示

    C:\Users\Freewind>node -v
    v0.8.11
    

    我是之前安装的，那时候的版本还是0.8.11

    来个hello world。直接输入node，就会出现交互运行环境：

    C:\Users\Freewind>node
    > console.log('hello, world')
    hello, world
    undefined
    >
    

    如果想执行某个js文件，可以输入：

    node test.js
    

    ## npm

    npm是nodejs中内置的包管理系统，类似于ubuntu上的apt-get，或者centos上的yum，或者ruby上的gem。

    我们安装完nodejs后，它就可以使用了。我们可在命令行中输入：

    npm
    

    就可以看到一些提示信息。它提供的命令很多，可用来安装、卸载某些第三方的库。npm中的库有很多，可到这里查看：[https://npmjs.org/](https://npmjs.org/)，当前显示有20000多个。

    ### package.json

    npm的配置放在名为package.json的文件中，它的内容是一个json字符串，基本内容如下：

    {
      "author": {
        "name": "Freewind"
      },
      "name": "mytest",
      "version":"0.0.1",
      "dependencies": {
        "grunt": "~0.3.17",
        "grunt-typescript": "0.0.8"
      }
    }
    

    其中name和version是基本信息，必须有。不过通常比较关注的是&#8221;dependencies&#8221;信息，它表示所在项目依赖于哪些第三方库。

    首先要讲一下npm是怎么来查找路径的。当我们执行一个npm命令时，比如`npm install typescript`，它会首先查当前目录下有没有`package.json`文件或者`node_modules`目录。如果有则直接使用，没有的话，到上级目录中寻找，直到根目录。这个特性可以让我们在一个项目的子目录中执行npm命令，十分方便。如果所有的上级目录都没有的话，则会以当前目录作为工作目录，创建一个node_modules目录，把安装的库放到里面。

    如果在当前或者上级某个目录中，有package.json文件，并且里面已经定义了一些依赖，我们可以直接使用这个命令，自动安装：

    npm install
    

    后面没有加参数，表示按package.json的要求来安装。

    还有一个参数比较重要:

    npm install somelib --save
    

    后面的`--save`可用来告诉nodejs，安装完指定库后，把该依赖信息写到package.json文件中（如果有的话）。

    更多的常用命令：

    <table border="1" cellspacing="0" cellpadding="2" width="800">
    <tbody>
    <tr>
    <td valign="top" width="201">
            npm install somelib
          </td>
    <td valign="top" width="599">
            将somelib这个库安装在当前或上级某目录下
          </td>
    </tr>
    <tr>
    <td valign="top" width="201">
            npm install somelib -g
          </td>
    <td valign="top" width="599">
            将somelib这个库安装为全局库，一些工具通常使用这种方式安装，比如typescript。安装完后就可以在命令行中直接使用库中的命令了，比如tsc（typescript的编译命令）
          </td>
    </tr>
    <tr>
    <td valign="top" width="201">
            npm remove somelib
          </td>
    <td valign="top" width="599">
            从当前或上级目录下移除somelib
          </td>
    </tr>
    <tr>
    <td valign="top" width="201">
            npm remove somelib -g
          </td>
    <td valign="top" width="599">
            从全局库中移除somelib
          </td>
    </tr>
    <tr>
    <td valign="top" width="201">
            npm install somelib -save
          </td>
    <td valign="top" width="599">
            在当前或上级目录中安装somelib，并将它添加到package.json中的依赖项里，十分方便
          </td>
    </tr>
    <tr>
    <td valign="top" width="201">
            npm install
          </td>
    <td valign="top" width="599">
            根据当前或上级目录中的package.json，自动安装缺失库
          </td>
    </tr>
    <tr>
    <td valign="top" width="201">
            npm list
          </td>
    <td valign="top" width="599">
            显示当前或上级目录中已经安装的nodejs库
          </td>
    </tr>
    </tbody>
    </table>

    ## Typescript

    实际上此时并不需要安装typescript的编译器，但是由于肯定会用到，所以一起装了。

    Typescript的编译器是以nodejs库的方式发布的。我们只需要使用该命令：

    npm install typescript -g
    

    即可。安装成功后，在命令行中输入：

    tsc
    

    它就会提示一些帮助信息。

    常用用法如下

    <table border="1" cellspacing="0" cellpadding="2" width="800">
    <tbody>
    <tr>
    <td valign="top" width="249">
            tsc test.ts
          </td>
    <td valign="top" width="551">
            编译一个test.ts文件
          </td>
    </tr>
    <tr>
    <td valign="top" width="249">
            tsc -declaration test.ts
          </td>
    <td valign="top" width="551">
            为test.ts生成它对应的声明文件，产生的文件为test.d.ts
          </td>
    </tr>
    <tr>
    <td valign="top" width="249">
            tsc -out dist.js test.ts 
          </td>
    <td valign="top" width="551">
            将test.ts编译为dist.js文件
          </td>
    </tr>
    <tr>
    <td valign="top" width="249">
            tsc -out dist test.js
          </td>
    <td valign="top" width="551">
            将test.ts编译成dist目录下，如果test.ts依赖其它文件，则把它们也编译到dist目录中
          </td>
    </tr>
    <tr>
    <td valign="top" width="249">
            tsc -module commonjs
          </td>
    <td valign="top" width="551">
            使用commonjs方式生成js（默认），还可以使用amd
          </td>
    </tr>
    </tbody>
    </table>

    关于typescript的更多内容，参看我另一篇博文。

    ## gruntjs

    gruntjs是一个构建系统，有点像java中的ant，但是感觉要简单很多，而且很多任务都是跟js相关的。它现在有380个插件，可在其主页[http://gruntjs.com/](http://gruntjs.com/)上查询。

    安装：

    npm install -g grunt-cli
    

    这里安装的是`grunt-cli`，它是一个全局的命令行工具，可以让不同的项目使用不同版本的grunt来编译，解决兼容问题。而`grunt`，则要以本地方式安装到我们的某个工作目录中。

    进入某个工作目录，输入以下命令：

    npm install grunt
    

    则会在本地安装grunt，之后我们才能使用grunt命令来进行一些操作。

    如果你是windows系统，一定要注意，安装完后，你有两个grunt命令可用，一个是grunt，一个是grunt.cmd，它们是两个不同的文件。其中前者是grunt库本身的命令，后者是用来执行你定义的任务的命令。请记住必须要用`grunt.cmd`，否则你会奇怪，为什么我的任务没执行等等。这个坑爹的问题花了我很多时间才解决。

    要生成它的配置文件，需要安装`grunt-init`工具：

    npm install grunt-init -g
    git clone https://github.com/gruntjs/grunt-init-gruntfile.git ~/.grunt-init/gruntfile
    

    然后运行：

    grunt-init gruntfile
    

    它会问一些配置方面的问题，然后生成一个默认的`Gruntfile.js`文件。

    将会在当前或上级目录（按nodejs的方式来定位工作目录），产生一个grunt.js文件，里面的内容如下：

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
        nodeunit: {
          files: ['test/**/*_test.js']
        },
        watch: {
          gruntfile: {
            files: '<%= jshint.gruntfile.src %>',
            tasks: ['jshint:gruntfile']
          },
          lib_test: {
            files: '<%= jshint.lib_test.src %>',
            tasks: ['jshint:lib_test', 'nodeunit']
          }
        }
      });

      // These plugins provide necessary tasks.
      grunt.loadNpmTasks('grunt-contrib-concat');
      grunt.loadNpmTasks('grunt-contrib-uglify');
      grunt.loadNpmTasks('grunt-contrib-nodeunit');
      grunt.loadNpmTasks('grunt-contrib-jshint');
      grunt.loadNpmTasks('grunt-contrib-watch');

      // Default task.
      grunt.registerTask('default', ['jshint', 'nodeunit', 'concat', 'uglify']);

    };
    

    可以看到它里面定义了很多任务，比如使用jslint检查js文件，合并，压缩，执行单元测试，监视文件变化等。注意里面的文件路径都是空的，需要根据自己的情况添加。

    要执行某个任务，可在命令行中输入：

    grunt.cmd qunit
    

    如果只输入：

    grunt.cmd
    

    则表示执行默认任务。此时表示'lint qunit concat min'。

    其中的`watch`任务非常有用，它可以监视某些文件和目录，当它们发现变化时，自动执行指定的某些任务。我们将可以使用该功能监视typescript文件，当修改后，自动把它编译为js，浏览器那边就能看到最新的代码了。

    注意，使用此任务时，最好在后面加上-force参数，即：

    grunt.cmd watch --force
    

    这样当构建过程中出现错误时，它会跳过并继续监视，否则就直接退出了。

    ## grunt-typescript

    最后是安装grunt-typescript插件，它可以在grunt中增加编译typescript的任务：

    npm install grunt-typescript --save
    

    然后[按照其文档](https://npmjs.org/package/grunt-typescript)往grunt.js里添加一些配置即可。我这里的成品是这样的：

    /*global module:false*/
    module.exports = function (grunt) {

        grunt.loadNpmTasks('grunt-typescript');

        // Project configuration.
        grunt.initConfig({
            concat: {
                WindBase: {
                    src: ['tmp/typescripts/controllers/**/*.js',
                        ''],
                    dest: 'public/javascripts/app.js'
                },
                wind_articles: {
                    src: ['../wind_articles/tmp/typescripts/**/*.js'],
                    dest: '../wind_articles/public/javascripts/module.js'
                }
            },
            min: {
                WindBase: {
                    src: ['public/javascripts/app.js'],
                    dest: 'public/javascripts/app.min.js'
                },
                wind_articles: {
                    src: ['../wind_articles/tmp/typescripts/module.js'],
                    dest: '../wind_articles/public/javascripts/module.min.js'
                }
            },
            watch: {
                files: ['typescripts/**/*.ts', "../wind_articles/typescripts/src/**/*.ts"],
                tasks: 'typescript concat min'
            },
            typescript: {
                WindBase: {
                    src: ['typescripts/src/**/*.ts'],
                    dest: 'tmp/typescripts',
                    options: {
                        module: 'amd', //or commonjs
                        target: 'es5', //or es3
                        base_path: 'typescripts/src',
                        sourcemap: false,
                        declaration: false
                    }
                },
                wind_articles: {
                    src: ['../wind_articles/typescripts/src/**/*.ts'],
                    dest: '../wind_articles/tmp/typescripts',
                    options: {
                        module: 'amd', //or commonjs
                        target: 'es5', //or es3
                        base_path: '../wind_articles/typescripts/src',
                        sourcemap: false,
                        declaration: false
                    }
                }
            }
        });

        // Default task.
        grunt.registerTask('default', 'typescript concat min');

    };
    

    注意其中的&#8221;typescript&#8221;和&#8221;concat&#8221;任务，可以设置多个子任务，名字（这里是WindBase和wind_articles）任选，只要里面的配置写对即可。你可以参照它修改。

    另外把package.json也贴上：

    {
      "name": "mytest",
      "version": "0.0.1",
      "dependencies": {
        "typescript": "~0.8.2"
      }
    }

## 完成效果

[![grunt-typescript](http://freewind.me/wp-content/uploads/2013/01/grunt-typescript_thumb.gif "grunt-typescript")](http://freewind.me/wp-content/uploads/2013/01/grunt-typescript.gif)
