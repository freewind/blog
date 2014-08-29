---
layout: post
title: Haxejs不适合与Angularjs一起使用
tags:
  - haxe
date: 2013-01-27 00:02:27
---

前些天成功地将Haxejs与Angularjs结合起来使用，并把之前用javascript写的几个angularjs的controller改成了haxejs，非常振奋。因为可以在一种typesafe的环境中写代码，是一件让人安心的事情。有以下几点让人感觉特别好：

1.  尽可能利用静态类型，在编译期就能找到各种笔误，并且编辑器的代码提示功能基本可用2.  可直接使用Haxejs提供的基本方法，如对String的操作、数组的操作等，不需要学习额外的js库3.  最后生成一个非常精简的js文件，去掉了所有没用上的代码，体积小

但随着使用的深入，一些不方便的地方慢慢显露出来，这主要是跟Angularjs的特性相关。

## 不断在两种语言中切换思路

Angularjs跟其它的前端mvc框架有很大的不同，比如它的很多代码是放在HTML标签上，直接引用$scope对象上的数据或方法，比如：

    <ul>
    <li ng-repeat="name in names">{{name}}</li>
    </ul>

    这里的names就是定义在$scope对象上的一个数组。因为我现在用haxejs来写controller，对于像这样的数据，都是在haxe中操作的，并且使用的是haxe的数据结构。虽然它们会被替换为javascript中相应的数据结构，但并不是一一对等的。比如这个names，我在haxe中使用的是Array<String>，它可近似地看作是javasript中的[]，但并不是完全对等。

    在上面的例子中，这段代码运行正常，但下面这个就不对：

    <div ng-show="names">
    todo
    </div>

    如果names是null或undefined或[]，这个层应该会隐藏起来。但是如果它是由haxe中的空数组转过来的，则还是会显示！因为names实际上是一个在js中定义的Array function的对象，而不是[]。正确的写法应该是：

    <div ng-show="names!=null && names.length>0">
    todo
    </div>

    这样的例子还有很多，所以在用haxe写angularjs代码时，并不能完全脱离javascript，反而等时刻在两种差别很大的语言中切换思路，经常要考虑：这个对象转成js后，能不能被angularjs识别，会不会出问题等等，让人感觉特别累。

    如果使用其它的框架，比如jquery等，则没有这样的问题。因为在haxe中，往往是通过一些代码得出最后的文本，然后替换掉html页面中的某个div。在这个过程中，不需要其它js的参与，基本可以用haxe的思路来处理整个流程，所以不会累。

    ## Haxe的变量名中不能有$

    Haxe是一门可以编译为多种语言的语言，因为有些语言不允许在变量名、方法名中出现$，所以haxe也不允许变量、方法名中出现$。然而Anguarljs却大量使用$，比如$scope, $http, $scope.$on, $scope.$watch等等，都是以$开名，用来表示这是由Anguarljs提供的供我们使用的对象。

    在haxe中，为了使用这些方法，我的代码需要写在：

    Reflect.field(scope, "$on")("myevent", function() {});

    而在js中只需要写成：

    $scope.$on("myevent", function(){});

如果只是一两处还好说，但是当我看angularjs代码满眼的$时，有种欲哭无泪的感觉。

如果使用其它的库，则不会出现这样的问题。因为jQuery使用了$，但仅仅使用它作为jQuery对象的别名，在haxe中可以直接使用jQuery这个名字即可，在方法的调用过程中，很少有$。而其它的库，都会避开与jQuery的冲突，也很少使用$。

综上所述，虽然使用haxejs可以让我们的代码享受到静态类型的便利，但在与Angularjs的结合中，付出的代价也很大，甚至超过了这种便利。在使用haxejs来写angularjs的controller类的这两天，感觉很烦燥，开发效率很低。

所以我觉得如果用Angularjs的话，最好使用一种采用javascript思路的语言（如coffeescript/typescript等），或者直接采用javascript本身。如果想用haxejs的话，最好使用一些较简单、对HTML侵入性较低的框架，如jQuery等。

这篇文章主要表达的是haxejs与angularjs（或其它对HTML侵入较多的js框架）之间不太合适，但haxejs本身产生js的能力还是很强的。在其它需要手写很多js代码、逻辑复杂的情况下，使用haxejs，还是很合适的，可以大幅提高开发效率并提高代码质量，例如后端代码（nodejs开发）可以考虑。因为你可以：

*   静态类型的保护及编辑器的提示*   Extension Method，可以让函数调用更加流畅*   haxe强大的宏功能，可以让代码更简洁*   可生成单个js文件，包含所有js代码，且去掉所有不需要的