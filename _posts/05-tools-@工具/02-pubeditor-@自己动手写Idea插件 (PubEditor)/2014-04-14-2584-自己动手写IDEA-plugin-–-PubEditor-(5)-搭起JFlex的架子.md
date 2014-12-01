---
layout: post
id: 2584
alias: write-idea-plugin-yourself-pubeditor-5-jflex
tags: Scala
date: 2014-04-14 22:55:10
title: 自己动手写IDEA plugin – PubEditor (5) 搭起JFlex的架子
---

在IDEA中，使用JFlex来写词法分析器最方便，因为IDEA提供了一个修改后的jflex，内置了对它的支持。

需要注意的是，由于IDEA提供的`idea-flex.skeleton`的实现内容跟原始的模板有一些区别，用法也不一样，我在这里花费了不少时间才搞清楚。具体参看：[http://stackoverflow.com/questions/23031925/whats-wrong-with-this-simplest-jflex-code](http://stackoverflow.com/questions/23031925/whats-wrong-with-this-simplest-jflex-code)

关于JFlex的内容，可以看官方文档：[http://jflex.de/manual.html](http://jflex.de/manual.html)，非常详细，看一遍下来就差不多了，然后再注意一下我上面提的那个问题，就差不多可以搭架子了。

不过JFlex相关的内容还是比较复杂的，特别JFlex生成的Java代码很难读，一些复杂的逻辑基本上没法看懂，我花了一天的时间也没弄懂，只好点到为止了。

等这个PubEditor完成后，将会好好研究一下，到时候再写一些东西。

## IDEA插件支持

IDEA中有插件支持jflex，可以语法高亮，查错，自动下载idea修改的jflex.jar，以及一键生成java代码，非常有用，必备插件。

安装以下两个插件：

*   JFlex support
*   Grammar Kit

## 创建pubspec.flex语法文件

下面在项目中创建一个`pubspec.flex`文件，内容如下：

```flex
package com.thoughtworks.pli.pub_editor.parser;

import com.intellij.lexer.FlexLexer;
import com.intellij.psi.tree.IElementType;
import scala.Some;

%%

%class _PubSpecLexer
%implements FlexLexer
%unicode
%type IElementType
%function advance
%{
private String matchedText() {
    return yytext().toString();
}
private Some<String> someText() {
    return new Some<String>(yytext().toString());
}
%}

Comment = "#" .*
NewLine = \r\n | \r | \n

%%

{Comment} { return PubTokenTypes.comment(matchedText()); }
{NewLine} { return PubTokenTypes.newLine(); }
.         { return PubTokenTypes.badCharacter(matchedText()); }
```

每个jflex定义文件，都分成三部分，以`%%`隔开。

### User code

第一部分是`user code`，我们可以添加自己的java代码，将原样写到最终生成的java文件中，一般用来放`package`,`import`之类的语句。

在我们的例子中，声明了所在的package和引入的类，这些类将会在下面的代码里用到。

其中`scala.Some`是因为我其它的代码是用scala写的，需要这个类。而｀FlexLexer｀和`IElementType`是IDEA提供的公共类，我们必须使用它们才能让IDEA识别生成的类。

### Options and declarations

第二部分是`options and declarations`，我们可以提供一些代码生成的设置，使用类似于正则表达式的方式定义一些字符类，还可以定义一些state，以及一些将嵌入到生成文件中的java方法。

生成的类名为`_PubSpecLexer`，使用这个名字是因为还会生成一个供外部使用的包装类:

```
%class _PubSpecLexer
```

将实现`FlexLexer`，这是IDEA要求的：

```
%implements FlexLexer
```

使用`unicode`来解析目标文件，这基本上是必须的：

```
%unicode
```

匹配到token时，返回哪种类型的对象。`IElementType`是IDEA提供的：

```
%type IElementType
```

自定义用于获取下一个token的方法名，默认是`yylex`，但为了配合`FlexLexer`接口的定义，需要改成`advance`:

```
%function advance
```

我们还可以定义一些变量或者方法供文件中的其它代码使用，它们将会成为生成的类的实例变量或方法：

```
%{
private String matchedText() {
    return yytext().toString();
}
private Some<String> someText() {
    return new Some<String>(yytext().toString());
}
%}
```

最后定义了两个字符类，使用了类似正则的表达式：

```
Comment = "#" .*
NewLine = \r\n | \r | \n
```

其中`Comment`将匹配所有以`#`开头的单行内容，`NewLine`匹配了三种换行符。

### Lexcial rules

第三部分将针对某一种词法定义，提供相应的java代码返回某个特点的token对象。

```
{Comment} { return PubTokenTypes.comment(matchedText()); }
{NewLine} { return PubTokenTypes.newLine(); }
.         { return PubTokenTypes.badCharacter(matchedText()); }
```

它们使用了自定义的`PubTokenTypes`类，其内容如下：

```
package com.thoughtworks.pli.pub_editor.parser

import com.intellij.psi.tree.IElementType
import com.thoughtworks.pli.pub_editor.PubLanguage
import scala.util.Try

class PubTokenTypes

object PubTokenTypes {
  def badCharacter(value: String) = new PubElementType("BadCharacter", Some(value))
  def comment(value: String) = new PubElementType("Comment", Some(value))
  def newLine = new PubElementType("NewLine")
}

class PubElementType[T](elementType: String, value: Option[T] = None) extends IElementType(elementType, PubLanguage) {
  override def toString = elementType + value.map(v => s" ($v)").getOrElse("")
}
```

可以看到内容比较简单，只是定义了一些方法生成特定的token对象。

匹配的时候，定义在前面的规则将会先使用，所以当匹配到最后一行，即:

```
.         { return PubTokenTypes.badCharacter(matchedText()); }
```

时，说明它不是我们认识的，直接生成一个相应的`badCharacter`类型的对象。

## 生成Java类

在`pubspec.flex`文件上点右键，选中`Run JFlex Generator`，插件会自动下载所需要的`jflex.jar`和`idea-flex.skeleton`文件，放在我们指定的位置，然后生成两个Java文件。

一个是`_PubSpecLexer`，主要的逻辑都在里面，代码很长，是典型的C风格代码（为了性能）。

另一个是`PubSpecLexer`，这是一个包装类，继承了IDEA提供的`FlexAdapter`类，供我们使用，其内容如下：

```
package com.thoughtworks.pli.pub_editor.parser;

import com.intellij.lexer.FlexAdapter;

import java.io.Reader;

public class PubSpecLexer extends FlexAdapter {
    public PubSpecLexer() {
        super(new _PubSpecLexer((Reader) null));
    }
}
```

注意这个类可能有编译错误，改成上面的就行了。下次再生成时，这个类不会重新生成。

## 如何使用

生成的代码如何使用呢？这里先不考虑插件如何使用，而是从普通调用的方式考虑。

这里给出示例代码：

```
package com.thoughtworks.pli.pub_editor.parser;

public class TryLexer {

    public static void main(String[] args) {
        String input = "#!!!!!\n#????\nabc";
        PubSpecLexer lexer = new PubSpecLexer();
        lexer.start(input);
        for (int i = 0; i < 10; i++) {
            System.out.println(lexer.getTokenType());
            lexer.advance();
        }
    }
}
```

需要注意的是，这种用法是IDEA修改后的JFlex代码的使用方法，而不是原始方法，这一点在本文最前面的那个问题里，已经说明了。

它会打印出以下内容：

```
Comment (#!!!!!)
NewLine
Comment (#????)
NewLine
BadCharacter (a)
BadCharacter (b)
BadCharacter (c)
null
null
null
```

可以看出，的确可以正确匹配上各Token.

## 源代码

源代码地址：

https://github.com/freewind/PubEditor

可以checkout出这个标签：`5_jflex_skeleton`

下一篇将会完善我们的词法定义。
