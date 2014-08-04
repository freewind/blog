title: AutoHotkey小试
tags:
  - More ...
date: 2012-04-24 23:07:23
---

今天尝试了一下auto hotkey这个工具，可以方便地定义一些快捷键，实现某些方便操作。

比如在xtend中，它用了两个奇怪的符号«»在文本中引用xtend代码。它们在xtend的编辑器中很好输入，直接按ctrl+shift+,即可。但在其它地方怎么输呢？

方法是这样的，按alt+171和alt+187，够麻烦的。

所以我定义了一个auto hotkey的脚本，来实现同样功能。

> ^+,::      
> sendinput, «»       
> sendinput, {Left}       
> return
> 
> ^+.::      
> sendinput, »       
> return
> 
>  

另外，还想实现这个功能：当我在eclipse中按了ctrl+s时，会自动刷新浏览器。思路时，先找到firefox/chrome/ie等，激活它们的窗口，发送刷新命令，然后再切回到eclipse。

代码如下：

> $^s::                                       ; only capture actual keystrokes      
> SetTitleMatchMode, 2                        ; match anywhere in the title       
> IfWinActive, Eclipse                        ; find Sublime Text       
> {       
>     Send ^s                                 ; send save command       
>     IfWinExist, Mozilla Firefox             ; find firefox       
>     {       
>         WinActivate                         ; use the window found above       
>         Send ^r                             ; send browser refresh       
>         WinActivate, Eclipse                ; get back to Sublime Text       
>     }       
>     IfWinExist, Google Chrome               ; find Chrome       
>     {       
>         WinActivate                         ; use the window found above       
>         Send ^r                             ; send browser refresh       
>         WinActivate, Eclipse                ; get back to Sublime Text       
>     }       
>     IfWinExist, Internet Explorer            ; find IE       
>     {       
>         WinActivate                         ; use the window found above       
>         Send ^r                             ; send browser refresh       
>         WinActivate, Eclipse                ; get back to Sublime Text       
>     }       
> }       
> else       
> {       
>     Send ^s                                 ; send save command       
> }       
> return

<p><font color="#666666"></font>
</p>