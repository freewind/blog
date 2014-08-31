---
layout: post
id: 1733
alias: solve-the-problem-that-idea-show-strange-square-instead-of-chinese-character-on-windows
tags: IDE
date: 2013-01-06 11:29:54
title: 如何解决Windows下 IDEA 中文字体显示为方框的问题的
---

## 老高问

请教下大家是怎么解决Windows下 IDEA 中文字体显示为方框的问题的？（先声明：我对Windows下的宋体完全无爱）

默认情况下代码编辑器内可以正常显示中文，但某些窗口，比如Message，Make，比如“配置对话框”，中文显示为方框。我试过改Console Font，也试过在IDEA用的`jre/lib/fonts`下增加fallback，都没效果。

Make输出后来想了想可能是javac返回给IDEA的，所以通过Compiler Properties增加参数 `-Duser.language=en -Duser.country=US -Duser.variant=US -Duser.region=US` 搞定了（改成输出英文）。但界面上其他中文显示为方框的问题，常规途径似乎无效。

## 老高答

搞定了。一定要 `override default fonts `

[![image](/user_images/1733-1.png "image")](/user_images/1733-1.png)

这里才是全局，一开始这些都是方块。
