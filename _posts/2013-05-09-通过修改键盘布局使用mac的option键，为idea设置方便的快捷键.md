---
layout: post
alias: change-mac-option-key-to-provide-more-keyshorts-for-idea
tags: Mac
date: 2013-05-09 21:11:14
title: 通过修改键盘布局使用mac的option键，为idea设置方便的快捷键
---

Mac上的辅助键有4个：control, option(alt), command, shift，比windows上多一个。（注意windows上的ctrl键的大部分功能，在苹果上是command）

在Idea中，发现很多option键+字母组合的快捷键不能用。比如我想像vim那样，用option加上hjkl来移动光标，但按option+j的时候居然没反应。使用其它的键又怕跟别人冲突，因为现在工作中经常需要结对编程。而在editor中按option+j的时候，它会输入一些奇怪的字符，完全浪费了这么一个好键。如果能把它利用起来就好了。

经过搜索，发现有人问了同样的问题，并且给出了多个解决方案。经过我的测试，最方便的是这个：[http://stackoverflow.com/a/16019737/342235](http://stackoverflow.com/a/16019737/342235)

简单来说，就是它里面提到的那个网站，自定义一份mac下使用的键盘布局方案。由于默认值已经把option处理好了，所以我们直接下载即可。下载后，把它拷贝到~/Library/Keyboard Layouts目录下，再在系统设置的“语言与文字”那里，找到并选中我们刚拷过去的那个方案名即可。

这时注意在右上角的输入法图标处，会多出一行，我们需要把它选中才能看到效果。（这里需要注意，因为我在这里浪费了半个小时）。然后再到Idea中试试，没有意外的话，就可以用option+字母来作为快捷键了。用它定义的快捷键，既简短，又很少会跟原有的冲突，非常好。
