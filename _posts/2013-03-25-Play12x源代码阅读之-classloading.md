title: Play1.2.x源代码阅读之 classloading
tags:
  - PlayFramework1
date: 2013-03-25 14:56:40
---

## play.classloading.ApplicationClasses

这是一个重要的容器，它内部有一个cache，保存项目中java源文件及字节码相关的内容。它在内部定义了一个类ApplicationClass，Play把从项目中扫描java文件，根据其文件、源代码、编译后的字节码、增强后的字节码等数据，生成一个个ApplicationClass实例，放在cache中。同时，ApplicationClass还提供了compile, enhance等方法，可将java源代码编译为字节码，再调用Play的各插件及enhancer，对字节码进一步增强。

为了将java源代码编译为字节码，它还持有一个ApplicationCompiler的实例，用于将java源代码编译为二进制的字节码。

## play.classloading.ApplicationCompiler

play为了得到更快的编译速度，以及对编译过程进行更多的控制，没有使用jdk中自带的javac，而采用了eclipse的jdt。

而这个ApplicationCompiler类，就是用于封装jdt相关的api。调用JDT时，我们需要向它传入一些编译参数和回调，在回调中可根据jdt传来的类名包名等，返回正确的源代码或源文件。

Play的采用的编译参数如下：

1.  org.eclipse.jdt.core.compiler.debug.lineNumber GENERATE2.  debug.sourceFile GENERATE3.  debug.localVariable GENERATE4.  codegen.unusedLocal PRESERVE

并忽略以下警告：

1.  problem.missingSerialVersion2.  problem.deprecation3.  problem.unusedImport

前面的几个设置很重要，因为play的很多魔术都要依赖它们，必须在生成的字节码中尽量保持源代码中的信息（如参数名，变量名，行号等）。

## play.classloading.ApplicationClassloader

该类是play框架中的核心类之一，继承于ClassLoader，负责play应用中类的导入。它的方法有一部分返回List<ApplicationClass>，即ApplicationClasses中定义的那个ApplicationClass，持有项目中java文件、源代码、字节码等数据，另一部分返回List<Class>，即ApplicationClass中相应的java字节码导入到jvm后返回的类。

在play的总管类play.Play中持有ApplicationClassloader的实例(play.Play#classLoader)。当某处代码需要载入类时，可直接通过Play.classLoader进行载入。ApplicationClassloader会首先判断是不是项目中的java类，如果是的话，则进行编译、增强，并放入缓存中；否则的话，交给上级classloader，即由jvm自己来处理载入。

ApplicationClassloader有一个ApplicationClassloaderState的对象，它可以看作一个自增id，用来标识ApplicationClassloader的当前状态。它定义了一个detectChanges方法，可用于检查项目中的java源代码是否增删改，以及引入的插件是否有变化（插件可对java进行增强，所以也需要考虑）。如果发现了变化，则会生成一个新的ApplicationClassloaderState，可让别人知道classloader的状态发生了变化。然后，ApplicationClassloader会尝试以hotswap的方式导入修改的类（轻量，很快），失败的话，会重新载入整个应用（等于重新启动play，比较慢）。

## play.classloading.ApplicationClassloaderState

用于标识ApplicationClassloader的状态类。它内部有一个static的AtomicLong的generator，和一个实例字段currentStateValue。每次生成一个新对象，都会取generator的下一个值赋给currentStateValue。由于AtomicLong保证了原子操作，所以ApplicationClassloaderState的每一个实例的currentStateValue值都不同。通过检查ApplicationClassloader中持有的ApplicationClassloaderState的实例，可以知道classloader状态是否发生了变化。

对于这个类，我有点小小疑问：直接在ApplicationClassloader中使用一个AtomicLong就可以了，为什么要专门定义一个这样的类呢？

## play.classloading.BytecodeCache

这是一个Cache工具类，它可以把每个java源代码及相应的字节码保存在项目的tmp/bytecode/目录下的某一个文件中，方便使用。在保存时，并不保存完整的源代码，而是将play的版本号、项目引用的插件名和源代码一起做一个md5运算，得到摘要，并保证每个字节的值都不为0，写在文件的前部；然后写一个byte 0作为分隔，然后把字节码写在后面。

这样在检查某一个java源代码是否有效时，实际上还要考虑其它可能影响最终字节码的因素。

## play.classloading.HotswapAgent

这是一个代码很少但不太好理解的类。它实现了类的hotswap，在内部它使用了jdk中提供的Instrumentation，对代码进行重载。以后将专门研究一下这个。

在ApplicationClassloader中发现java源代码发生了变化需要重新载入时，会首先调用该类尝试重载变化的类，失败后才会重载整个应用。

如果JRebel是免费的话，这里应该可以调用JRebel以实现更有效的重载。

## play.classloading.hash.ClassStateHashCreator

ClassStateHashCreator是一个工具类，它的作用是给定若干路径后，去搜索里面所有的java源文件，然后通过正则表达式去找到所有形如: class X 这样的类定义，然后把它们组合成一个字符串。这样Play就可以快速检测到用户定义的java类是否发生了变化。