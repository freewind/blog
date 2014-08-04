title: 动态改变类：Javassist
tags:
  - Java
date: 2011-09-12 23:37:00
---

这个是从playframework里看到的，作者使用了这个Javassist的库，对代码进行了动态增加，实现了很多非常便捷的功能。

这个javassist，可以让我们修改一个java类的字节码。我们知道这样的工具不少，但是它的优点，是可以让我们用文本写一段java代码，然后就插到已有的字节码里去了。

比如说，已经有一个类叫Hello，定义如下：

```
public class Hello() {
    public String hi() {return "hi";}
}
```

我们可以得到这个类编译后的字节码，然后使用javassist给它增加一个函数：

```
CtClass ctClass = getBytecodeOf("Hello");
   String bye = "public static String bye() { return \"bye\" }";
   ctClass.addMethod(CtMethod.make(bye, ctClass));
}
```

这样，这个Hello类实际上就有了一个bye()的函数了。

play使用它，成功地实现了&#8221;充血模型&#8221;，使我们不需要dao。基本思路是：

定义一个基类，叫Model，里面定义了一些函数如find(), save(), delete()，但是为空，它们的作用就像接口一样，只是为了让我们在程序中可以调用这些函数。我们定义的model都继承于它。当这些model导入时，使用javassist对这些类进行增强，把之前为空的代码都换成事先定义好的代码。这样，我们即可以调用它们，又实现了真正所需的功能，这个做法真的是非常方便。

这只是其中的一个例子。可以说play的基础就是javassist，它的很多魔术般的功能都来自于它。一旦理解之后，我们也可以自己实现一些方便好用的功能。