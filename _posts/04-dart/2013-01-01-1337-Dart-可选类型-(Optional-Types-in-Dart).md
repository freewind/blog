---
layout: post
id: 1337
alias: dart-optional-types
tags: 其它语言
date: 2013-01-01 17:24:58
title: Dart 可选类型 (Optional Types in Dart)
---

(本文译者是唐古拉山itang 2012-07-27)

http://www.dartlang.org/articles/

作者 Gilad Bracha

2011 十月 [原文](http://www.dartlang.org/articles/optional-types/)

Dart最大的创新之一是对可选类型（optional type）的使用。这篇文档旨在诠释可选类型是怎么工作的。

_内容_

[概貌](http://shuzu.org:9000/dart/books/articles-and-tutorials/articles/5011364fe4b0d1017825aad7#Overview)

[静态检查器](http://shuzu.org:9000/dart/books/articles-and-tutorials/articles/5011364fe4b0d1017825aad7#section2)

[类型Dynamic](http://shuzu.org:9000/dart/books/articles-and-tutorials/articles/5011364fe4b0d1017825aad7#section3)

[范型](http://shuzu.org:9000/dart/books/articles-and-tutorials/articles/5011364fe4b0d1017825aad7#section4)

[受检模式](http://shuzu.org:9000/dart/books/articles-and-tutorials/articles/5011364fe4b0d1017825aad7#section5)

[使用类型](http://shuzu.org:9000/dart/books/articles-and-tutorials/articles/5011364fe4b0d1017825aad7#section6)

##### 概貌

Dart语言是动态类型的。你可以写没有任何类型标注的程序，然后运行之， 跟在JavaScript里一样。

你也可以选择添加类型标注到你的程序里：

*   添加类型不会阻止你的程序编译和运行 - 甚至你标注的是不完整的或者完全错的。*   你的程序将有一样准确的语义， 不管你加不加类型标注

增加类型标注到代码里还是有益处的。类型提供了一下的好处：

*   给人准备了文档。如果合理的放置类型标注，让人更容易阅读你的代码。*   给机器准备了文档。工具能够在各种方式下利用好类型标注。尤其是他们能帮忙完成一些漂亮的功能，如IDE下的代码自动完成和改进导航。*   早期的错误检测。Dart提供了一个静态检查器，它能就潜在的问题给出警告， 不会阻止你前行。另外，在开发模式下，Dart自动转换类型标注为辅助调试的运行时断言检查。*   有时，在编译为JavaScript时类型能帮助改进性能。这个我们待会再详细说说。

##### 静态检查器

静态检查器扮演的角色跟C语言的lint很像。编译时给出关于潜在问题的警告信息。很多警告是跟类型相关的。静态检查器_不会_产生错误，就是说你能一直编译和运行你的代码，而不用管检查器给出的结果如何。

这个检查器对每种可能的类型违反都不会如邻大敌。它不是一个类型检查器(typechecker)， Dart使用类型的方式跟传统的类型系统就不一样。检查器只会抱怨可能真的会出现的问题， 但是不会让你为了满足这个narrow-minded类型系统而让你赴汤蹈火。

例如， 考虑以下下面的代码：

    String s1 = '9';
    String s2 = '1';
    ...
    int n = s1 + s2;  // 译者注(1)
    print(n);

    这里存在一个明显的问题。静态检查器在这种情况下也会发出一个警告。该注意的是这段代码能照常运行(译者注：受检模式下将抛出异常），将`n`设置`‘91’`和打印出`91`。

    然而，还有不像一个典型的强制类型系统的，代码如下：

    Object lookup(String key) {...} // a lookup method in a heterogenous table
    String s = lookup('Frankenstein');

    以上代码的问题检查器是熟试无睹的。这是因为，尽管缺少类型信息，这段代码（译注：运行逻辑上）极有可能是正确的。像我们作为程序员，经常是具备一些语义方面的认识， 但是类型检查器不会。你知道通过'Frankenstein' 查存储的表所得的值是一个字符串，就算lookup方法申明返回的是`Object`。

    ##### 类型Dynamic

    当不提供类型时Dart如何避免抱怨？这里的关键就是类型`Dynamic`，它是程序员没有显示指明类型时的默认类型。使用类型``Dynamic`让检查器不会再多管闲事。

    有时，你会希望明确的使用`Dynamic`。

    Map<String, Dynamic> m = {
      'one': new Partridge(),
      'two': new TurtleDove(),
      ...,
      'twelve': new Drummer()
    };

    此处，我们也可以为`m`变量声明`Map<String, Object>`类型， 但是， 当我们去访问m的内容时，它具有了静态类型`Object`, 这样暴露给我们的东西很少了。这是因为map的内容只有通过`Object`的接口， 所以我们可能更倾向用`Dynamic`。如果我们试图调用map值的方法， 如下所示：

    pearTree = m['one'].container();

    如果'one'对应的值是Object类型，那么将得到警告信息，因为`Object`不支持container方法。 如果使用类型`Dynamic`, 警告就自动消除了。

    ##### 泛型

    Dart支持物化泛型。就是说，泛型类型的对象们在运行时持有它们的类型参数。将类型参数传给泛型类型的构造器是一个运行时操作。那么泛型跟声称的类型可选如何共存呢？

    好吧，如果你对类型想都不想去想， 泛型也不会强迫你什么。你能泛型类的实例而不用提供类型参数。例如：

    new List();

    工作得也好。当然，你可以这样写：

    new List<String>();

    如你所想。而

    new List();

    是以下这个的简写形式

    new List<Dynamic>();

    在构造器中，类型参数充当运行时角色。他们实际上是在运行时传递的，所以当你做动态类型判定时你能使用他们。

    new List<String>() is List<Object> // true: 所有的字符串都是对象
    new List<Object>() is List<String> // false: 不是所有的对象都是字符串

    在Dart里，泛型是符合程序员直觉的。这里有一些更有趣的示例：

    new List<String>() is List<Int> // false
    new List<String>() is List      // true
    new List<String>() is List<Dynamic> // 跟上面一样
    new List() is List<Dynamic>     // true, 他们是完全一样的

    相比之下，类型标注（例如,类型添加到变量的声明， 或者作为函数和方法的返回类型）扮演的是非运行时角色，而且不会影响程序语义。还有一个示例值得学习：

    new List() is List<String>      // 也是true

    你可能写程序不带类型，但是你经常将数据传递到的是带类型的库里。避免类型带来的干扰， 泛型不带类型参数的被视为相应的任意泛型版本的子类型。

    ##### 受检模式

    在开发阶段， Dart程序可以以受检模式（Checked mode）运行。

    如果在受检模式下运行程序，系统将自动执行某种类型检查，诸如在传递参数时，返回结果时，执行赋值时。如果检查失败， 执行将在那个点停止，并给出清晰的错误信息。所以，

    String s = new Object();

    将停止代码的继续执行， 因为`Object`不是`String`的子类型。然而

    Object foo(){ return "x"; }
    String s = foo();

    工作得却很好， 因为foo实际返回的对象类型在运行时类型就是`String`, 虽然foo的类型签名说返回的是一个`Object`。当一个对象分配给一个变量时, Dart会检查对象的运行时类型是不是变量声明（静态）类型的子类型。

    本质上， 受检模式就像在带有观察点的调试器下运行你的程序，它会在每次赋值，返回值等情况下进行子类型的检查。看下面更多的例子：

    <int>[0,1,1][2] = new Object(); // 受检模式下失败

    bar(int n) { return n * 2; }
    ...
    bar(3.2); //产品模式下返回6.4, 受检模式下失败

    在受检模式下，每次当一个参数传给一个函数， 参数的类型会被检查，看它是否是形参声明的类型的子类型。我们能轻松的写出正确的代码：

    bar(num n) { return n * 2; }
    ...
    bar(3.2); // 工作正常

    int i_bar(num n) { return n * 2; }
    ...
    i_bar(3.2); // 受检模式下失败， 因为返回的值不是一个整形int

    注意最后一行。类型检查在返回值那一刻起作用， 就算函数的返回值没有分配给任意变量。

    让我们再回到老朋友Frankenstein那里。

    Object lookup(String key) {...} // 查表
    String s = lookup('Frankenstein');

    如果正如我们预想的那样，lookup方法返回一个字符串，受检模式将顺利的执行下去。如果我们错了， 它会捕获错误抛给我们。 在产品模式下， 这份代码正常运行，没有异样。假定lookup实际上是返回类`Frankenstein`的一个实例， 不是一个字符串，这是变量s仍然持有这个实例。Dart不会在任何情况下试图用魔法将它转换为一个字符串。如果它这样做了，那意味着类型标注改变了我们程序的行为，而且类型不在是可选的了。

    当然， 如果你根本就没用类型，受检模式也不会当你的路。

    my_add(s1, s2) { return s1 + s2; }

    my_add(3, 4); // 7
    my_add("3", "4"); // "34"

    所有这些检查造成可观的性能损失， 所以在生产环境下这是一种不可接受的负担。 这些检查带来的好处是可以跟踪动态类型错误，帮助更好的调试中存在的问题。这些问题中的大部分通过各种测试可以侦测到， 但是受检模式帮助定位他们。

    ##### 使用类型

    怎么用类型取决于你自己。如果你讨厌类型，你根本不需要用它们。你也不会得到任何类型警告， 并且你能在这种开发方式下获得跟其他动态语言下那种舒服的感觉。你也可以从类型中受益， 因为Dart库有类型签名， 能告诉它们所需要的和所返回的。如果你在受检模式下运行，给库传递了错误的参数， 受检模式能侦测你犯错误的点。

    如果你喜欢类型，你可以在任何地方使用它们，这就像在静态类型语言中一样。然而，尽管你获取不到同静态类型检查一样的级别。Dart的规则更宽松。我们预期里会提供一些额外的工具来，让解释类型变得跟严格， 满足喜欢它们的人的口味。

    我们不推荐走极端化。在合适的地方使用类型。你能做的最有价值的事情是在你的库的公开成员上面加上类型。再下一步，在私有成员上做同样的事情。就算没专人维护的代码，你会发现当你留下一堆代码，几周或几个月之后你再反过头来看它们的时候，就能知道它带来的帮助。在这两种情况下， 你不一定必须将类型添加到方法体或函数体里。 类型签名总能给库的使用者带来价值， 即使他们不是100％准确。

    在函数体里面，可能不需要过于关于类型标注的声明。有时， 代码本来就很简单，类型不是真正的问题所在， 反而像来搅局的。

    通常的， 你应该专注设计， 不让它受类型方面的考虑所影响。例如， 在需要传函数名字符串的地方用传函数代替 这使你的代码更有效更方便类型检查。Dart不鼓励无节制的使用反射。然而， 当真正需要反射的时候， 你应该毫不豫犹的用它。

    ###### 译者注：

    1、 Dart is removing support for concatenating strings with the + operator， 此段代码可以改写如下：

    int n = “${s1}${s2}"
