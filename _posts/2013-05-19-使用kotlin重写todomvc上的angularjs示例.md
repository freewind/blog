title: 使用kotlin重写todomvc上的angularjs示例
tags:
  - 其它语言
date: 2013-05-19 00:55:09
---

经过几天的努力，终于成功把todomvc上的angularjs示例，由原生javascript代码，改成了Kotlin代码-即用Kotlin来写代码，然后把它编译成js代码。整个过程比较流畅，有一些地方值得记录下来。

首先说下Kotlin。Kotlin是一个极其低调的项目，一直在默默地开发，如果不是主动关注的话，都不知道它现在开发到了什么状态，而且至今还没有推出1.0版。这可能跟作者的个性有关，感觉其作者是一个比较温和及低调的人。

Kotlin现在的状态如下：

1.  基本可用，语法层面已经比较完善，已经看到有一些小项目（比如Idea插件acejump）使用它来开发
2.  它的IDE支持不错，高亮、格式化、错误检查、基本的代码重构等，都已经提供，而且用起来不卡（不像scala或者xtend)
3.  Kotlin的卖点之一&ldquo;生成javascript&#8221;目前看来基本可用，但与第三方框架集成，还有一些不方便的地方，主要是不能生成JSON形式的代码，以及不能方便的传递函数
4.  生态环境还没有建立起来，比如web框架、一些成熟java项目的wrapper、比较有亮点的库等，目前都没有看到
5.  在语法层面上，还缺少一些比较有用的特性，比如传递函数值、macro、或者xml/json的语法支持（这个是我希望直接支持的）

不过正因为Kotlin的低调，以及IDEA这些年在高端开发人员中的流行，让我相信它比较靠谱。毕竟它的出发点&rdquo;代替java作为IDEA的开发语言&ldquo;，我想对于Jetbrains公司来说，应该是一个强需求。而Kotlin在开发过程中的稳扎稳打，小步前进，让我们用起来会比较放心：提供了的都是好用的。

这里正好跟它的竞争对手xtend进行一下对比, Xtend跟Kotlin的行事作风正好相反。当它推出2.x（还是3.x?)版的时候，让人误以为已经是一个成熟可用的版本了，哪知一试，到处都bug。编辑器支持不好，连基本的格式化功能都没提供，随便写几行代码就能遇到一堆bug，IDE还经常卡死。开发团队似乎只顾着开发&rdquo;next big feature&#8221;，我当时提的一些很基本的issue，值到一年以后才部分修复。所以我现在基本上不再相信Xtend，虽然更喜欢它&ldquo;生成java源代码而非字节码&rdquo;这个功能。

言归正传，说一下Kotlin的&ldquo;生成Javascript&#8221;代码的功能。作为Kotlin的卖点之一，现在只能说基本可用。它目前基本上还没有考虑与第三方js库的集成，而主要是&rdquo;把Kotlin中定义的类及代码以自己特定的方式转为Javascript，并可以操作DOM及Canvas&rdquo;。希望以后Kotlin提供某种方式，能够更好的支持那些比较成熟的前端框架，让代码写起来更加自然。

Kotlin代码转js代码，目前采用的是code to code，所以我们没有办法使用Java或Kotlin自己提供的jar库。如果我们想使用第三方Kotlin库，需要把它们的源代码加到自己的项目中，并且要保证它没有用到不支持的包和类，且能正确编译。这种方式比我预期中的弱很多，但在实际使用中，发现并不成问题，因为已经有很多好用的js库可供使用，我们只需要定义一些native接口就可以直接用了，还是非常方便的。

下面是一些与js库集成时必须注意的问题：

1.  某些js函数要求参数是一处JSON形式的对象，但Kotlin不能生成那种格式，怎么办。

Js code:

```
function save(user) {}
save({ name: "Freewind", age: 100 })

```

在Kotlin中，首先要生成一个native接口：

```
native fun save(user: User)

```

这里自定义了一个类型User。怎么定义它才对呢？最直观的写法是这样的（错的）：

```
class User(val name:String, val age:Int)
save(User("Freewind", 100))

```

代码很简洁，可惜这样生成的是Kotlin特有的类代码，不能被save识别，因为它会发现 user.name和user.age都是undefined。(实际上Kotlin生成的 $name以及get_name和set_name）。

正确的写法如下：

```
native("Object") class User {
    var name:String = noImpl
    var age: Int = noImpl
}

```

其中的noImpl是由Kotlin提供的一个占位符，如果在native中使用它，并不会对生成的代码产生影响，因为用native声明的类并不会生成对应的js代码，它们仅仅起到辅助类型检查的作用。

然后：

```
var user = User()
user.name = "Freewind"
user.age = 100
save(user)

```

它生成的js代码如下（伪代码）：

```
var user = new Object(); // TODO: new function() ?
user.name = "Freewind";
user.age = 100;
save(user);

```

这样代码虽然啰嗦一点，但生成的js代码能用。目前只能这样做了，希望以后Kotlin能提供一种更直接的转换方式。

1.  如何引用一个已经存在的js对象或方法

比如引入了angular.js后，在js中就可以直接使用angular这个对象。在Kotlin中，我们应该这么做：

```
native trait Angular {
    fun module(name:String, deps: Array[String])
}
native var angular: Angular = noImpl

```

即使用native来声明一个已经存在的对象，同时为了以类型安全的方式调用它的方法，还要定义一个native的trait。

如果想让声明的变量的名字跟原有的不同，可以这么写：

```
native("\$") val jq: Any = noImpl
native("_") val underscore : Any = noImpl

```

同理，如果是function，可以这样：

```
native fun forEach(...)

```

1.  如何给array增加方法？

Kotlin为js提供的array，缺少了很多js中对应的方法。我们可以用native的方式补全它们：

    ```
native fun <T> Array<T>.push(x: T) = js.noImpl
native fun <T> Array<T>.splice(i1: Int, i2: Int) = js.noImpl
native fun <T> Array<T>.indexOf(x: T) = js.noImpl
native fun <T> Array<T>.filter(x: (T)->Boolean) = js.noImpl
native fun <T> Array<T>.forEach(x: (T)->Unit) = js.noImpl
native val <T> Array<T>.length: Int = js.noImpl

```

2.  如何传一个函数当参数

Js中经常有一些参数实际上是函数类型的，比如：

    ```
function filter(arr, test) {}

```

这里的test，实际上可以看成是一个 (item)->Boolean的函数。我们在Kotlin中，可以这么写：

    ```
native fun filter<T>(arr: Array<T>, test: (T)->Boolean)
filter(array(1,2,3), { it>1 })
filter(array(1,3,3), { x -> x>1 })

```

由于在Kotlin中定义一个可传递的function很不方便，它的标准格式是这样的：

    ```
val test = { (a:String, b:Int, c:User) -> true }

```

如果我们需要声明类型的时候，必须要写外面的大括号和里面的小括号，当嵌套较多时，代码可读性很差。如果能让我们写成这样：

    ```
val test = { a:String, b:Int, c:User -> true }

```

或者

    ```
val test = (a:String, b:Int, c:User) -> true

```

就好了，可惜现在还不支持。虽然我们可以换一种方式：

    ```
native fun doit( crazy: (a:String, b:Int, c:User) -> Boolean)

```

那就可以写成：

    ```
doit { a,b,c -> ... }

```

但实际上这种方式并没有那么好用，因为很多js库的参数都非常灵活，很难在native定义时，给它指明各个类型，这么做太别扭了。
3.  初始化代码
<p>在Kotlin中，不能在全局域中调用方法，也就是说，你不能这么写：

    ```
val app = angular.module("test", array())

```

而必须这么做：

    ```
fun main() {
    val app = angular.module("test", array())
}

```

另外，Kotlin中定义的代码，都会放在一个module中，在js里也没有办法直接调用，必须选取得其module。上面那段代码，我们必须在html或者某个js文件中，手动调用它：

    ```
<script type="text/javascript">
    Kotlin.modules["some-module"].main();
</script>

```

基本问题这么多，更多的是要在实际写代码时体会。在改写todomvc的代码时，开始遇到了很多错误，但当我把它们一个个解决以后，再刷新页面，一下子全都运行正常了，让我当时有点难以相信。

项目代码放在这里：[http://github.com/freewind/kotlin-angularjs](http://github.com/freewind/kotlin-angularjs)，有兴趣的同学可以看看。

最后想说的是，等Kotlin再成熟一些之后（半年？一年？），可以尝试使用它来同时写前后端代码。可以跟第三方js框架结合（比如angularjs等），甚至会有某个Kotlin框架直接把前后端搞定。之前用js写Angularjs代码时，当业务逻辑一旦复杂，我就很难记得一个scope里到底定义了多少字段和多少函数，而用Kotlin时可以声明好，大大减少了记忆量和手误。

由于这次的尝试还比较顺利，我将会在以后用它写一些更复杂的程序，尝试用Kotlin同时搞定前后端。