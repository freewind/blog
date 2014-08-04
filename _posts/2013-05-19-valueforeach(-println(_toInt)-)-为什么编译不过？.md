title: value.foreach( println(_.toInt) ) 为什么编译不过？
tags:
  - Scala
date: 2013-05-19 21:28:52
---

这其实是一个困扰我许久的问题，看起来十分简单。

Scala代码：

    val value = Some("100")
    value.foreach( println(_.toInt) )   // !!! can't be compiled

    它还给出了错误提示：

    > missing parameter type for expanded function ((x$1)=>x$1.toInt)

    按我之前的理解，下划线_可用来代替前面提供的参数，所以上面第二句应该等价于：

    value.foreach( x => println(x.toInt) ) // 这一句可以编译

    既然后来这个可以编译，为什么前面的写法不行？

    在stackoverflow网友和scala群友（特别感谢mustang）的帮助下，终于基本上搞清楚了这个问题，我觉得值得记录一下。

    首先， _.toInt 是一种简写，它等价于：

    (x) => x.toInt

    这里的x的类型没有指定，它靠scala推导。所以它必须依赖它所属的函数，看它需要什么类型的参数。

    如果是这样的代码：

    value.foreach( _.toInt )

    就没有问题，这是因为，它等价于：

    value.foreach ( x => x.toInt )

    由于value是一个Option[String]，所以foreach的参数类型是(String)=>Unit，所以x的类型被成功推导为String，没有任何问题。

    当_.toInt放在println中，代码就等价于：

    println( x => x.toInt )

    问题在于println的参数类型是：

    println(x:Any)

    并没有办法为x指定一个具体的类型，所以上面的代码就出错了。

    再看一眼println(_.toInt)，似乎它可以有两种展开方式：

    println( x => x.toInt )
    x => println(x.toInt)

    如果是后一种，放在value.foreach中，不就没有任何问题了吗？

    没错，但是正因为存在多种可能的展开方式，所以scala必须做出一种选择，最终它选择了简单的第一种。

    总结规则如下：

    1. 如果_后面有调用，比如_.toInt, _.toString这样的，那么它就是展开的单位

    2. 如果_作为整体占位符，比如println(_), 那么所在的函数就是展开单位

    所以:

    println(_.toInt) 将会展开为 println( (x) => x.toInt )
    println(_) 将会展开为 (x) => println(x)
    user.setName(_)将会展开为 (x) => user.setName(x)