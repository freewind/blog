---
layout: post
alias: my-hax-project-haxe-angular-support
tags: haxe
title: 发布了一个Haxe的小项目：HaxeAngularSupport
date: 2013-01-22 21:35:58
---

经过几天的奋斗，终于把这个小项目做出来了：[https://github.com/freewind/HaxeAngularSupport](https://github.com/freewind/HaxeAngularSupport)

这个东西是一个haxe的宏，它的作用是在编译时期改变我们的代码结构，自动生成一些手写麻烦的代码，让我们写起来简单一些。这个项目的代码之前不是这个样子的，但由于一个关于宏的bug还未修复，之前的想法无法实现。好在今天发现了另一种简单的办法，绕开了那个问题，终于成功。

现在haxe官方发布的稳定版是2.10，近期将要发布3.0版。其中有关宏的api正在大幅增强，所以有bug是难免的，期待3.0的发布。

下面讲讲这个AngularSupport的宏的作用。之前我们用haxe写angularjs的代码，使用的是传统方式（也是angularjs推荐的方式）：

    class MyCtrl implements Public {
        public static function __init__() {
            js.Lib.eval("MyCtrl.$inject = ['$scope', '$http'];");
        }
        function new(scope:Scope, http:Dynamic) {
            scope.name = "Freewind";
            scope.hello = function() {
                js.Lib.alert(scope.name);
            }
        }
    }
    private typedef Scope = {
        name:String,
        hello: Void -> Void
    }

    这种结构对于javascript来说是比较理想的，因为完全没有&#8221;this&#8221;的调用，减少了混乱，所有的操作都是针对scope来进行的。但是放在haxe中，则有点不太好：

1.  我们需要在最下面另外定义一个typedef，指明Scope所有可用的字段和方法，以获得静态类型的检验和编辑器的提示
2.  没有利用到class结构中的字段与方法，所有的操作都是在构造函数中完成的。这样我们无法在编辑器的“大纲”视图中看到类结构，以及快速定位到某字段或方法
3.  每次增加一个字段或方法，都需要修改两处

    我希望按普通的方式来写：

    class MyCtrl implements Public, implements AngularSupport {
        @AngularSupport({inject:['$scope', '$http'], scope:'$scope'})
        function new(scope:Dynamic, http:Dynamic) {
            this.http = http;
            // don't need to assign "scope" to anything
        }
        var http:Dynamic;
        var name = "Freewind";
        function hello() {
            js.Lib.alert(name);
        }
    }

你可以看到，以前放在scope上的东西，现在全都以field/method的方式放在class中了。我们对数据进行操作时，都指向this，而不必像之前那样，必须写个&#8221;scope.xxx&#8221;。

最大的好处是结构更加清楚了，当代码多起来的时候，感觉就会比较明显了。

这段代码实现了接口&#8221;AngularSupport&#8221;，所有实现该接口的类，都会在编译期得到增强。

由于它使用了最新的macro api，所以在稳定版的2.10 haxe上用不了，需要下载最新的nightly-build。对于遗留项目有风险，所以最好等到3.0正式发布后再在生产项目中使用，尝鲜的话无所谓了。

具体要求和用法，请参考项目的README：[https://github.com/freewind/HaxeAngularSupport](https://github.com/freewind/HaxeAngularSupport)

(更多理论上的内容，可参考：http://blog.csdn.net/rocks_lee/article/details/9129319)
