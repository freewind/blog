---
layout: post
id: 14
alias: install-virtual-box-for-fedora-on-windows
tags: Linux
date: 2011-09-12 23:32:58
title: Fedora结局篇 windows中安装Fedora虚拟机
---

经过这几天的试用，对Fedora有了一个比较直观的了解。与windows相比，两者各有优势与不足。
<p>Windows的强项在于各种各样好用的日常软件：输入法、浏览器、英汉词典、QQ、讯雷，等等，windows这边完胜。不足之处在于命令行模式，提供的功能太弱，想自己做一些复杂的事情很难。
<p>Fedora15的Gnome3图形界面表现还不错，但在日常软件上，明显薄弱。前面提到的那些软件，在Fedora上要么没有，要么功能简单不好用，我弄了几天找不到顺手的，后来还是在Linux中安装一个windows虚拟机，里面再装上这些软件。这个虚拟机，平时基本上要占用15%左右的cpu，比较耗资源。而且不太稳定，出现多次无法关机或者文件损坏的情况。
<p>Fedora的强项在于命令行模式，功能太强大了，对于编程工作很有帮助。
<p>所以最终我放弃了在Fedora中安装windows虚拟机的做法，反过来在windows中安装一个Fedora的虚拟机，这样就可以取两家之长处。Fedora安装好以后，修改启动文件，只以命令行模式启动，这样只占用不到2%的cpu，非常省资源（如果开图形界面的话，大约是30%靠上）。
