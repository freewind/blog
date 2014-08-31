---
layout: post
id: 1037
alias: android-broken-png-image
tags: Android
date: 2012-10-30 20:15:28
title: Android步步惊心 之 破碎的渐变色图片(png格式)
---

最初遇到此问题时，我以为是我那500元的廉价Android手机屏幕有问题，显示的颜色种类不够，导致一张渐变色的png图片，变成了条状：

[![image](/user_images/1037-1.png "image")](/user_images/1037-1.png)

一边暗自诅咒“便宜没好货”，一边打开了手机上的浏览器连到电脑上，查看该图片，没想到一切正常：

[![image](/user_images/1037-3.png "image")](/user_images/1037-3.png)

原始图片是什么样子呢？如下：

[![image](/user_images/1037-5.png "image")](/user_images/1037-5.png)

注意看最右边的颜色要比左边深一点，平滑的渐变色。

意料之中，又是意料之中，我又遇到了一个android bug。其实完全说bug也不对，这是android为了减少渲染图片的时间，默认这么处理的。但坑爹之处在于，你到是给个简单的设置让我们选择如何渲染啊。根据不完全统计，有以下这些方式可以解决这个问题：

1.  <strike>在xml中，为图片创建一个xml格式的drawable，然后把dither设为true。</strike> （经测试无效）2.  在java代码中，取得该图片，并设为true。（未测试）3.  <strike>getWindow().setFormat(PixelFormat.RGBA_8888);</strike> （经测试无效）

可以看出这还应该算是一个bug，不然怎么会有这么多五花八门的解决方案呢。

最终成功的解决方法，相当之诡异：

> 把该图片的透明度设为99%，重新保存。其它什么也不用动。

则一切正常。

不能不承认，能想到此方法的人真是天才，这得经过多少次的推测与尝试，才能找到这个方法。据说它之所以能成功解决，是因为满足了两个条件：

1.  png图片中开启了alpha通道（透明）2.  至少有一个像素使用了该alpha通道

于是，干脆直接把整张图片直接设为99%，简单粗暴。反正人眼也是看不出那1%的透明度的。

[![image](/user_images/1037-7.png "image")](/user_images/1037-7.png)

连正常显示一张图片的功能都能做得如此跌宕起伏，android，真有你的！

参考资料：

1.  [Why android lose image quality when displaying a png file?](http://stackoverflow.com/questions/13137735/why-android-lose-image-quality-when-displaying-a-png-file/13138028#13138028)2.  [android:dither=“true” does not dither, what's wrong?](http://stackoverflow.com/questions/4769885/androiddither-true-does-not-dither-whats-wrong)3.  [Bitmap quality, banding and dithering](http://www.curious-creature.org/2010/12/08/bitmap-quality-banding-and-dithering/)
