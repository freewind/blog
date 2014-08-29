---
layout: post
title: Android之残缺的神器：android-x86 + vmlite
tags: Android
date: 2012-10-18 22:47:02
---

Android应用的开发让人苦不堪言，于是找到了一些神器，残缺的神器。之所以残缺，是因为它们还不够完美，只能在大多数情况下有用。

这里要介绍的，是 **Android-x86 + vmlite**

Android运行于手机等移动设备，使用的芯片与普通电脑的CPU不同，所以Android不能在电脑上运行。但是[Android-x86](http://www.android-x86.org/)这个国产项目，却可以让Android运行在电脑里。不过因为它也是一种操作系统，不能像普通软件一样安装，通常装在虚拟机如Virtual-Box中才能使用。它有什么用呢？

我们在虚拟机中开一个Android后，它就像是一个普通的Android模拟器，我们在开发时可直接把项目部署上去运行。相对于Android模拟器的龟速，它就是一只不睡觉的兔子。相于对真机，它的速度也快上很多，而且可以自定义分辨率。

<font color="#cccccc">广告：</font>[<font color="#cccccc">本博主最爱的vpn，每年60元，每月10G流量，不限速。博主已使用一年多。</font>](http://www.vpnst.com/152.html)

Google提供的Android模拟器，速度是非常慢的。启动慢，运行慢，上传代码也慢。如果是几M，上传时间还可接受，但当到了20M左右时，上传一次的时间都够你吃顿午饭了。我一直搞不明白，为什么Android的虚拟机效率如此之低，要知道我的电脑可是i7级别的，但仍然慢得想杀人。如果使用真机会快很多，20M的部署时间也能控制在30秒内，尚可接受。但是使用Android-X86，上传并启动应用的时间能在3秒左右完成，没错，3秒。

但Android-x86提供的镜像文件，安装之后几乎没法使用。虽然能启动，但是鼠标不灵，几乎没法控制。virtual-box的增强包在Android-x86中无法安装，所以在虚拟机与母机之间切换鼠标时，还需要按切换键。尝试的结果让人非常沮丧，因为几乎没法使用。光定位鼠标的时间就大于使用真机的时间了，而且这种体验非常影响人的心情。

正当我在放弃之时，在stackoverflow上的朋友的帮助下，发现了vmlite提供的修改版。vmlite是一个收费的虚拟机项目，但是作者提供了android-x86的修改版，完美地解决了上面提到的两个问题，使用起来非常舒适。

Vmlite提供的修改版地址：[http://www.vmlite.com/index.php?option=com_content&view=article&id=69&Itemid=178](http://www.vmlite.com/index.php?option=com_content&view=article&id=69&Itemid=178)

下载后解压，里面有两个文件是我们要用的，一个是已经安装好Android的镜像文件 VMLite-Android-v4.0.4.vmdk，另一个是sdcard的镜像文件 sdcard.vmdk

[<font color="#cccccc">广告：由衡天小张提供的博客空间，快速稳定，售后也不错，相当满意</font>](http://my.hengtian.org/aff.php?aff=1312)

先截一张图，这是在Virutal-Box中安装好之后的效果，里面是Android 2.3.x，屏幕分辨率设为480&#215;800：

[![image](http://freewind.me/wp-content/uploads/2012/10/image_thumb.png "image")](http://freewind.me/wp-content/uploads/2012/10/image.png)

安装过程对于熟手来说不难，先装好Virtual-Box，再建一个虚拟机，再导入那两个镜像文件即可。对于新手可能还有些难度，可参照vmlite地址中的步骤。等我慢点再写一个完整的安装教程出来。

但装好之后，默认的屏幕大小是800&#215;600的，而且不可更改。这是因为它已经把这个分辨率写死在启动文件中了，就算我们手动使用VirtualBox提供的命令修改了镜像文件的分辨率，也不会起作用。解决这个问题比较麻烦，需要有一定linux能力的人才能操作，基本思路是在linux系统中，mount这个虚拟机文件，然后找到启动文件，修改里面的一个参数。

具体步骤参看这个问题，很详细的解决过程：[http://stackoverflow.com/questions/12493599/how-to-change-the-screen-size-of-vmlite-android](http://stackoverflow.com/questions/12493599/how-to-change-the-screen-size-of-vmlite-android)
