title: java中的Continuation实现：commons-javaflow
tags:
  - Java
date: 2011-10-19 16:21:07
---

在看play的libs.F时，发现它使用了一个叫commons-javaflow的库，来实现continuation，即将某一个程序的运行暂停，将现场保护起来，等晚点再读回来接着运行。

其网址为：[http://commons.apache.org/sandbox/javaflow/index.html](http://commons.apache.org/sandbox/javaflow/index.html)

最后更新日期为2008年，最新版本为1.0-snapshot，看来又是一个夭折的项目。

这里是一个简单的说明页面，讲解了其工作过程：[http://commons.apache.org/sandbox/javaflow/tutorial.html](http://commons.apache.org/sandbox/javaflow/tutorial.html)

从这里我们可以看出，它还是很神奇的。当我们在代码中运行了Continuation.suspend()时，它就会把所有的局部变量和stack保存在一个Continuation对象里，马上跳出整个run方法。我们只要有了这个continuation对象，就可以在以后的任意时刻，运行Continuation.continueWith(c)继续执行刚才的代码，就好像中间没有断开过一样。

正是因为它能做到如此神奇的功能，play才会使用它来做到Promise：当需要的数据还没有准备好的时候，就把它暂停并保存好，腾出线程做别的事情。等数据准备好之后，再把那个对象取回来恢复，继续执行。

因为这样的功能肯定不是java自己可以提供的，所以javaflow也使用了一些修改字节码的库，必须先对字节码进行增强，才能正常运行。它提供了两种方式：

1.  使用一个ant任务预增强
2.  使用它提供的一个ContinuationClassLoader类，指定需要增强的jar，在运行期增强

我尝试了第二种方法，可惜的是，不论我怎么调试，都会报ClassNotFoundException，无奈放弃。

Play对其代码进行了裁减，没有使用ant和ContinuationClassLoader，可能用到了其它的方法实现。

到处都是魔法代码啊。