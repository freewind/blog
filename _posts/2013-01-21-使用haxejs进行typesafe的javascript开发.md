---
layout: post
title: 使用haxejs进行typesafe的javascript开发
tags:
  - JavaScript
date: 2013-01-21 23:27:42
---

我使用angularjs写网页，当逻辑复杂的时候，需要写很多js代码。我对js一直比较怕，因为有这几个问题困扰着我：

1. Javascript在语言层面有很多陷阱，很容易犯错
2. 有时候笔误写错了什么，没有运行到它的时候发现不了
3. 当代码比较多的时候，想重构难以下手
4. 核心中提供的常用方法比较少，需要引入很多第三方库来实现功能，增加了文件体积

所以我一直想找一种语言可以代替js，动静态都行，但要能有效解决上面的几个问题。

我考察了以下几种：

- [Dart](http://www.dartlang.org)
- [Typescript](http://www.typescriptlang.org)
- [Coffeescript](http://coffeescript.org/)
- [haxejs](http://www.haxejs.org)
- [clojurescript](https://github.com/clojure/clojurescript)

它们都是可以生成js代码在浏览器端运行的。

最终我的选择是Haxejs，这里讲一下选择的原因以及一些示例供参考。

1.  熟悉并且喜欢clojure的，不妨直接上clojurescript。写的是clojure代码，但能生成javascript。群里的老猪同学用它，感觉还不错。
2. Coffeescript是很有名的。它的思路还是Javascript，但通过自己的语法规避了不少js的陷阱。喜欢ruby又不追求静态类型的同学可以用它。对于多层嵌套的代码，利用它简化的语法，可以让嵌套不再那么痛苦。
3. Dart, Typescript, Haxejs都是动静态类型的，语法及Javascript/Java都有点像。想使用类结构来组织javascript代码的，可使用它们中的任何一种。微软系的同学可能会用Typescript，它的思路也是javascript，只是变了种写法。Dart的思路是dart，它会产生比较大的额外js代码实现自己的基础类库，介意的同学需要考虑一下。Haxejs的思路是Haxe，它虽然定义了自己的数据结构，但在生成js代码时，可以去掉用不到的，生成的js代码极小。

关于Haxe，对于普通的使用者我并不是很推荐，因为它现在有以下问题：

1. 产品化比较差，官网上的文档少，资料少，严重依赖看源码。对于英语不好的同学来说，入门不易
2. 同时它可以生成七八种语言的代码，所以源代码看起来有点头晕，到处是#if #end这样的编译开关
3. 编辑器的支持也不够好，处于“能用”状态
4. 使用haxe的人大多是被它的一个很强的nme跨平台框架吸引，使用它的js功能的人倒不是很多。

但这并不能掩盖Haxe的优点：

1. 它是动静态类型的，我们可以利用编辑器与编译器的类型检查，写代码比较放心
2. 我试过的各语言中，只有它能完全去除用不上的js代码，产生的文件极小。这意味着我们可以在代码中无所顾忌地使用各种第三方haxe库
3. 它有强大的macro功能，可以让我们按自己的想法改变代码的写法。比如[我实现的一个宏可用于减少angularjs的写法](https://github.com/freewind/HaxeAngularSupport)，或者杨博的[一个库](https://github.com/Atry/haxe-continuation)可以让我们以同步的方式写代码，却生成callback多层嵌套的js代码

通过检查haxe生成的js代码，可以看出使用了最常用的js用法，所以兼容性不是问题。同时它也内置了对jquery的支持，生成的是jquery的调用，也可以做到浏览器的兼容。

其实我以前就曾经试过Haxe，但当时感觉比较失望，因为相比dart，似乎并没有太多吸引人的地方。但是我看到了杨博的一篇文章：[Haxe+Node.js+continuation打造高性能高开发效率服务器架构（下）](http://www.ac.net.blog.163.com/blog/static/13649056201210243595589/)后，改变了自己的看法，因为如果能掌握haxe的macro，就有可能实现出以前做不到的功能。比如杨博的那个库，就是我以前研究nodejs时很想要的一个东西。

所以真正让我想用haxe的，也是因为haxe的macro，然后才发现了更多的优点（以及不爽之处）。因为Angularjs特殊的流程，使得它无法很好利用类结构带来的优势。比如这段代码，可以看作是angularjs的标准Controller写法：

```js
function Ctrl($scope) {
    $scope.name = "Freewind";
    $scope.hello = function() {
       alert($scope.name);
    }
    $scope.method1 = function() {}
    $scope.method2 = function() {}
    $scope.method3 = function() {}
    $scope.method4 = function() {}
    $scope.method5 = function() {}
}
```

对于Angularjs来说，这个Ctrl函数，仅仅是用来在调用一次后，对传入的$scope进行改变，比如增加属性和方法等，然后Ctrl和$scope就没关系了。如果想用引入类，大约是这样的：

```haxe
class Ctrl {
   public function new(scope:Scope) {
      scope.name = "Freewind";
      scope.hello = function() {
         alert(scope.name);
      }
      scope.method1 = function() {}
      scope.method2 = function() {}
      scope.method3 = function() {}
      scope.method4 = function() {}
      scope.method5 = function() {}
   }
}
typedef Scope = {
    name:String,
    hello:Void->Void,
    method1: Void->Void,
    method2: Void->Void,
    method3: Void->Void,
    method4: Void->Void,
    method5: Void->Void
}
```

可以看出来，这段代码跟前面的差不多，虽然引入了class，但却没有办法利用上class的字段、方法等结构，还是全部在一个new函数里操作。同时为了利用编译器的检查，还要另外为scope定义一个type hint，既繁琐又不好看。（虽然看起来有点繁琐，但对于很长的javascript代码，这样做还是有莫大的好处。因为毕竟只有少数代码是用来定义类型的，大部分代码可以受益）

如果我们的目的仅仅是这样，那么使用dart/typescript/haxe中的任意一种，都可以做到。但我希望可以写成这样：

```haxe
class Scope {
    public var name:String = "Freewind";
    public function hello() { alert(this.name); }
    public function method1() {}
    public function method2() {}
    public function method3() {}
    public function method4() {}
    public function method5() {}
}
```

看起来清楚多了，但问题是，无法实现。

这时候我想到haxe的宏(macro)不知道能不能派上用场。因为在杨博的例子中，可以看出macro的威力是巨大的，它可以把平行的代码变成嵌套的，这个改变相当的大，而不是局部优化。我能不能也写一个宏，但这个Scope类上定义的字段和方法，统统转移给传入的那个scope变量呢？这样我们写出来的是具有类结构的haxe代码，而产生的是angularjs需要的代码，皆大欢喜。

于是我开始了我痛苦而又有挑战性的macro之旅。此处省略三天废寝忘食的描述，并且对back2dos和simon等多位国际友人及haxe前辈表达深深的谢意，没有你们的帮助，我简直寸步难行。

最后的结果是，我差一点点就实现了这个功能。因为在此期间，发现了haxe编译器的一个bug：[http://code.google.com/p/haxe/issues/detail?id=1401](http://code.google.com/p/haxe/issues/detail?id=1401)，也正是这个bug，让我的功能没法实现。因为在编译期haxe对于类型的推断有问题，导致在改变代码结构的时候，无法正确的推断出某些方法的类型，编译不通过或者生成的js代码有误。

我的代码放在：[https://github.com/freewind/HaxeMacroQkdny](https://github.com/freewind/HaxeMacroQkdny "https://github.com/freewind/HaxeMacroQkdny")，有兴趣的同学可关注。我现在只能等待官方修正了这个bug后，才能继续尝试。

如果这个功能实现了，我们就可以实现这样的效果：

**haxe代码**

```haxe
@AngularScope("$scope")
class Ctrl {
    @Inject("$http") private var http;
    public var name = "freewind";
    public function hello() {
        trace(name);
    }
}
```

**Js代码**

```js
function Ctrl($scope, $http) {
    $scope.name = "freewind";
    $scope.hello = function() {
        console.log($scope.name);
    }
}
```

现在所能做的，只有等待了。如果这个功能实现的话，使用haxejs来开发angularjs就很爽了。

## 更新

这个功能已经实现了，使用了一种与这里描述的不一样的方式，绕开了那个bug。

参看：[发布了一个Haxe的小项目：HaxeAngularSupport](http://freewind.me/blog/20130122/2016.html)