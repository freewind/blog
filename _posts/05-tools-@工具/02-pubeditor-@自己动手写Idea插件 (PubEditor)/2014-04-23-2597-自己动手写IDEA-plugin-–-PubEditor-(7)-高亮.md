---
layout: post
id: 2597
alias: write-idea-plugin-yourself-pubeditor-7-highlight
tags: 未分类
date: 2014-04-23 14:19:35
title: 自己动手写IDEA plugin – PubEditor (7) 高亮
---

做了这么多准备工作，激动人心的时候终于要一个个到来了。首先上场的是语法高亮，这个是比较简单的。

## 效果图

![](/user_images/2597-0.png)

## Highlighter

我们在Idea编辑器中看到的每一个单词或者token，并不是一个简单的字符串，实际上是一个对象，定义了文本内容，前景色后景色，各种文字效果（加粗、斜体、下划线），以及不同状态下的效果（悬停、出错、选中）。

我们要定义一个Highlighter，它将使用前面定义的Lexer对代码进行解析，然后根据不同的token类型，提供一些颜色定义。

代码如下：

    package com.thoughtworks.pli.pub_editor.highlight

    import com.intellij.lexer.Lexer
    import com.intellij.openapi.editor.colors.TextAttributesKey
    import com.intellij.openapi.fileTypes.SyntaxHighlighterBase
    import com.intellij.psi.tree.IElementType
    import com.thoughtworks.pli.pub_editor.parser.PubSpecLexer
    import com.thoughtworks.pli.pub_editor.parser.PubTokenTypes
    import com.intellij.openapi.editor.DefaultLanguageHighlighterColors

    class PubHighlighter extends SyntaxHighlighterBase {

      object keys {
        val Comment = newKey("Pub.Comment", DefaultLanguageHighlighterColors.LINE_COMMENT)
        val KnownKey = newKey("Pub.KnownKey", DefaultLanguageHighlighterColors.KEYWORD)
        val UnknownKey = newKey("Pub.UnknownKey", DefaultLanguageHighlighterColors.INSTANCE_FIELD)
        val Value = newKey("Pub.Value", DefaultLanguageHighlighterColors.MARKUP_ENTITY)
        val String = newKey("Pub.String", DefaultLanguageHighlighterColors.STRING)
        val Unknown = newKey("Pub.Unknown", DefaultLanguageHighlighterColors.BLOCK_COMMENT)

        def newKey(keyId: String, color: TextAttributesKey): TextAttributesKey = {
          TextAttributesKey.createTextAttributesKey(keyId, color)
        }
      }

      def getHighlightingLexer: Lexer = new PubSpecLexer

      def getTokenHighlights(tokenType: IElementType): Array[TextAttributesKey] = {
        import PubTokenTypes._
        tokenType match {
          case Comment => Array(keys.Comment)
          case OneLineOfMultiLineString => Array(keys.String)
          case BadCharacter => Array(keys.Unknown)
          case KnownKey => Array(keys.KnownKey)
          case MultiLineStringKey | ParentKey | InlineKey => Array(keys.UnknownKey)
          case _ => Array()
        }
      }

    }
    

    代码比较直白，不多讲

    ## HighlighterFactory

    然后我还要建立一个HighlighterFactory，注册到Idea，告诉它当编辑pubspec.yaml文件时，使用我们提供的Highlighter。

    class PubHighlighterFactory extends SyntaxHighlighterFactory {

      def getSyntaxHighlighter(project: Project, virtualFile: VirtualFile): SyntaxHighlighter = {
        new PubHighlighter
      }

    }
    

    然后在plugin.xml中添加以下代码：

    <lang.syntaxHighlighterFactory key="pub" implementationClass="com.thoughtworks.pli.pub_editor.highlight.PubHighlighterFactory"/>
    

    其中的`key`的值很重要，一定是我们定义的language名称，否则Idea没法匹配。其值跟这里定义的Language相同：

    class PubLanguage extends Language("pub")
    

    ## 对认识的key特殊对待

    [Pub的文档](https://www.dartlang.org/tools/pub/pubspec.html)中定义了哪些key是它所识别的，对于这个key我们可以以特殊的颜色显示，跟其它的key区分开。

    以下key是可识别的：

    "name", "version", "description", "author",
    "homepage", "documentation", "dependencies", 
    "dev_dependencies", "dependency_overrides"
    

    为了跟其它key分开，我们必须定义一种新的token类型`KnownKey`，同时把由Lexer解析出来的`InlineKey`, `ParentKey`和`MultiLineStringKey`根据其值转换为`KnownKey`类型。

    首先定义一个`PubWithKnownKeyLexer`:

    class PubWithKnownKeyLexer extends PubSpecLexer {

      val KeyTypes = List(InlineKey, ParentKey, MultiLineStringKey)
      val KnownKeys = List(
        "name", "version", "description", "author",
        "homepage", "documentation", "dependencies", "dev_dependencies", "dependency_overrides"
      )

      override def getTokenType: IElementType = {
        val tokenType = super.getTokenType
        if (KeyTypes.contains(tokenType) && KnownKeys.contains(getKey(getTokenText)))
          PubTokenTypes.KnownKey
        else tokenType
      }

      private def getKey(token: String) = StringUtils.substringBefore(token, ":").trim.toLowerCase
    }
    

    然后修改`PubHighlighter`，让它返回新的Lexer:

    def getHighlightingLexer: Lexer = new PubWithKnownKeyLexer

这样就大功告成了。

## 源代码

上传至[http://github.com/freewind/PubEditor](http://github.com/freewind/PubEditor), 本篇标签为`7_highlight`
