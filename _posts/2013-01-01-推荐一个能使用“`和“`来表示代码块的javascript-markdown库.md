---
layout: post
title: "推荐一个能使用“`和“`来表示代码块的javascript markdown库"
tags: JavaScript
date: 2013-01-01 17:23:03
---

Markdown的标准语法中，对于代码块，使用“在每行行首添加4个空格”的方式来表示。

但这种方式对于长的代码块不太方便，因为我们常常需要手动调整缩进。而如果使用以下方式：

    ```
    my code
    ```

    则会方便很多。

    推荐：

    [https://github.com/chjj/marked](https://github.com/chjj/marked)

    可同时用于服务器端和浏览器端。

    浏览器端使用方法如下：

    下载[https://github.com/chjj/marked/raw/master/lib/marked.js放入本地并导入到html中，然后：](https://github.com/chjj/marked/raw/master/lib/marked.js%E6%94%BE%E5%85%A5%E6%9C%AC%E5%9C%B0%E5%B9%B6%E5%AF%BC%E5%85%A5%E5%88%B0html%E4%B8%AD%EF%BC%8C%E7%84%B6%E5%90%8E%EF%BC%9A)

    var html = marked.parser(marked.lexer(input));
    alert(html);
