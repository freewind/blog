---
layout: post
id: 2595
alias: write-idea-plugin-yourself-pubeditor-6-simplify-yaml-syntax
tags: 未分类
date: 2014-04-22 22:05:27
title: 自己动手写IDEA plugin – PubEditor (6) 实现简化的yaml语法规则
---

YAML是一种看起来很易读、格式简单的语法格式，但实际上，它的[语言规范](www.yaml.org/spec/1.2/spec.html)相当复杂，甚至连读一遍文档都是非常痛苦的事情。同时在IDEA上也没有找到可用的YAML插件，这让我很头痛，看起来一个很简单的任务突然变得复杂到无法实现。

## 简化的文法

经过短暂的纠结，我决定按照pubspec.yaml上给出的例子，实现一个简化的YAML语法规则，只支持以下几种格式：

*   注释，如

        # this is comment
    
*   单行key value，如
    key: value
    
*   嵌套key value, 如
    key:
      key1: value1
      key2:
        key3: value3
        key4: value4
    
*   多行文本，如
    key: >
      this is
      multiline
      string
    

    对于其它格式的内容，直接当作不识别的文本处理，这样简化后，词法规则的规模小了很多。

    ## 缩进处理

    但是马上又遇到另一个头疼的问题：YAML的文法实际上并不完全是“上下文无关”的，因为它的很多词法成分实际上跟它前面的缩进数量是相关的。而我使用的词法生成器工具JFlex只支持“上下文无关”文法，必须对缩进进行一些额外的处理才行。

    好在看到了一篇文章，讲如何在JFlex中处理缩进，对于我要实现的简化的缩进来说，还是可行的: [http://matt.might.net/articles/standalone-lexers-with-lex/](http://matt.might.net/articles/standalone-lexers-with-lex/)

    ## JFlex规则

    经过两天的努力和反复的尝试，终于实现了以下规则：

    package com.thoughtworks.pli.pub_editor.parser;

    import com.intellij.lexer.FlexLexer;
    import com.intellij.psi.tree.IElementType;
    import java.util.*;

    %%

    %class _PubSpecLexer
    %implements FlexLexer
    %unicode
    %type IElementType
    %function advance
    //%debug
    %{
    IndentationStack indentationStack = new IndentationStack();
    public int currentIndentation() { return indentationStack.current(); }
    public List<Integer> allIndentations() { return indentationStack.all(); }
    %}

    Comment = "#" .*
    LineSeparator = \r\n | \r | \n
    Indentation = {WhiteSpace}+
    WhiteSpace = [\ ]
    NonWhiteSpace = [^\ ]
    BlankChar = [\ \t\f]
    NonBlankChar = [^\ \r\n\t\f]
    ParentKey = {NonBlankChar}+ ":" {BlankChar}* {LineSeparator}
    InlineKey = {NonBlankChar}+ ":" {BlankChar}+
    InlineValue = .+
    MultiLineStringKey = {NonBlankChar}+ ":" {BlankChar}* ">" {LineSeparator}

    %state $newLine
    %state $value
    %state $multiLineString
    %state $multiLineStringAfterIndentation
    %state $inlineValue
    %state $multiChildren

    %%
    <YYINITIAL> {
        {Indentation}    { indentationStack.push(yytext().length()); return PubTokenTypes.Indentation(); }
        {Comment}        { return PubTokenTypes.Comment(); }
        {ParentKey}      { yybegin(YYINITIAL);return PubTokenTypes.ParentKey(); }
        {InlineKey}      { yybegin($inlineValue); return PubTokenTypes.InlineKey(); }
        {MultiLineStringKey} { yybegin($multiLineString); return PubTokenTypes.MultiLineStringKey(); }
        {LineSeparator}  { return PubTokenTypes.LineSeparator(); }
    }
    <$inlineValue> {
        {InlineValue}    { yybegin(YYINITIAL); return PubTokenTypes.InlineValue(); }
    }
    <$multiLineString> {
        {Indentation}    {
                             int currentIndent = yytext().length();
                             if(currentIndent >= indentationStack.current()) {
                             yybegin($multiLineStringAfterIndentation);
                                 return PubTokenTypes.Indentation();
                             } else {
                                 yypushback(currentIndent);
                                 yybegin(YYINITIAL);
                             }
                         }
        {NonWhiteSpace}+ { yypushback(yytext().length()); yybegin(YYINITIAL); }
        <$multiLineStringAfterIndentation> {
            {NonBlankChar}+ {  return PubTokenTypes.OneLineOfMultiLineString(); }
            {LineSeparator} {  yybegin($multiLineString); return PubTokenTypes.LineSeparator(); }
        }
    }
    .                    { return PubTokenTypes.BadCharacter(); }
    

    可以看到对于这小小的文法，居然需要这么多定义和代码。除去文法相关的定义，有不少代码是关于处理缩进的。不过由于上面的代码比较直白，所以就不多解释，如果看起来有不明白的地方，最好再看一遍JFlex的文档：[http://jflex.de/manual.html](http://jflex.de/manual.html)

    ## 测试

    最后上一个测试：

    String input = "#!!!!!\n" +
            "#????\n" +
            "abc: \"sss\"\n" +
            "xxx:\n" +
            "  aaa: 111\n" +
            "  bbb: 222\n" +
            "yyy: >\n" +
            "  1111111111111\n" +
            "    2222222222222\n" +
            "  3333333333333\n" +
            "zzz: ZZZ\n" +
            "  ccc: This line actually is invalid\n" +
            "???";
    PubSpecLexer lexer = new PubSpecLexer();
    lexer.start(input);
    while (true) {
        IElementType tokenType = lexer.getTokenType();
        if (tokenType == null) {
            System.out.println("--- end ---");
            return;
        }

        System.out.println(tokenType + "(" + lexer.getTokenText() + ")");
        lexer.advance();
    }
    

    运行结果如下：

    Comment(#!!!!!)
    LineSeparator(
    )
    Comment(#????)
    LineSeparator(
    )
    InlineKey(abc: )
    InlineValue("sss")
    LineSeparator(
    )
    ParentKey(xxx:
    )
    Indentation(  )
    InlineKey(aaa: )
    InlineValue(111)
    LineSeparator(
    )
    Indentation(  )
    InlineKey(bbb: )
    InlineValue(222)
    LineSeparator(
    )
    MultiLineStartFlag(yyy: >
    )
    Indentation(  )
    OneLineOfMultiLineString(1111111111111)
    LineSeparator(
    )
    Indentation(    )
    OneLineOfMultiLineString(2222222222222)
    LineSeparator(
    )
    Indentation(  )
    OneLineOfMultiLineString(3333333333333)
    LineSeparator(
    )
    InlineKey(zzz: )
    InlineValue(ZZZ)
    LineSeparator(
    )
    Indentation(  )
    InlineKey(ccc: )
    InlineValue(This line actually is invalid)
    LineSeparator(
    )
    BadCharacter(?)
    BadCharacter(?)
    BadCharacter(?)
    --- end ---

## 源代码

代码在[http://github.com/freewind/PubEditor](http://github.com/freewind/PubEditor)，标签为`6_other_basic_rules`
