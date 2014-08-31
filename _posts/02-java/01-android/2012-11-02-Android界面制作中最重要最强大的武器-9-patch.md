---
layout: post
alias: android-9-patch
tags: Android
date: 2012-11-02 12:57:02
title: Android界面制作中最重要最强大的武器: 9-patch
---

这段时间做Android项目，其中感觉最头疼的一点就是界面的制作。面对设计师发过来的效果图，想要做出与之完全相符的效果，真是头疼万分。

因为Android不像iphone那样，只需要面对一两种屏幕，所以大部分图片可直接截图使用，甚至绝对定位。而Android中，需要考虑到不同的屏幕大小以及dpi，直接使用原图图片基本上是没法使用的。

之前由于刚刚使用android，只能使用笨办法。

对于简单的，使用xml格式来描述效果。比如要在一张图片加一个圆角外边框，我使用以下代码来描述这样的一个边框：

<pre class="csharpcode"><shape xmlns:android=<span class="str">"http://schemas.android.com/apk/res/android"</span>>
    <stroke android:width=<span class="str">"1dp"</span> android:color=<span class="str">"#6D6866"</span>/>
    <padding android:left=<span class="str">"1dp"</span> android:top=<span class="str">"1dp"</span> android:right=<span class="str">"1dp"</span> android:bottom=<span class="str">"1dp"</span>/>
</shape>```
<style type="text/css">
.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }</style>

过程很繁琐，因为需要手动取色，量边框宽度，padding距离等等，鼠标可得一个像素一个像素的移动，这个过程能把人的眼睛盯瞎。

而对于一些难以模拟的效果，只好把图片切成一块一块的，然后在android繁杂无比的xml描述文件中，一点点拼起来。

这两种方法都非常痛苦和低效，经常一个简单的页面都要做上一天，然后还要在不同的屏幕分辨率下，手动调整长度、边距等数据，让我真不想再做android了。

直到前两天我发现了9-patch图片，才发现我之前的做法多么笨。关于9-patch，之前我也听说过，就是把一张图片分成三行三列的9宫格，在放大缩小的时候，四个角不变，其它部分伸缩。这样只要图片在设计时注意一下，出来的图片就可以轻松适应各种分辨率及大小，都有不错的显示效果。

但因为html不支持这种图片方式，所以我一直没用过，不知道它到底能做到什么程度。经过两天的使用，发现它可以使用简单的步骤，就实现出一些复杂的效果，以前一天的工作量，现在可以在一两个小时能完成，大大提高了开发效率。它是android界面实现中，最重要最强大的武器，那“遁去的一”。

9-patch又叫9-slice，下面以例子讲解一下。首先看一个圆色按钮：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb.png "image")](http://freewind.me/wp-content/uploads/2012/11/image.png)

如果把它整个当作一个按钮的背景，则在缩放的过程中，会变形，很难看：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb1.png "image")](http://freewind.me/wp-content/uploads/2012/11/image1.png)

仔细观察图片，发现变形难看的地方，主要是四个角。如果能在缩放过程中，保持4个角不变，应该会得到比较好的效果。可把它看作由下面三行三列组成：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb2.png "image")](http://freewind.me/wp-content/uploads/2012/11/image2.png)

这样在变形时，如果保持4角不变，会产生以下效果：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb3.png "image")](http://freewind.me/wp-content/uploads/2012/11/image3.png)

相比前面的，效果要好很多吧。

不光是简单的按钮，其它很多效果也可以使用9-patch实现。比如下面这个效果（只看边框）：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb4.png "image")](http://freewind.me/wp-content/uploads/2012/11/image4.png)

它可以用两个9-pacth图片实现，分别为：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb5.png "image")](http://freewind.me/wp-content/uploads/2012/11/image5.png)[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb6.png "image")](http://freewind.me/wp-content/uploads/2012/11/image6.png)

图片最外面的两条黑线先不管，后面会讲解。仔细看右图，发现上面有一条蓝线，正是前面图中间的分隔线。

再看这里：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb7.png "image")](http://freewind.me/wp-content/uploads/2012/11/image7.png)

这种很炫的图片，又有渐变，上面还有带阴影的分隔，照样一个9-patch图片搞定。

还有带阴影的边框：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb8.png "image")](http://freewind.me/wp-content/uploads/2012/11/image8.png)

中间的颜色可忽略，关键是它四周淡淡的阴影。只要使用这个图片，就可以轻松的给各种大小的图片套上一个阴影边框。

诸如此类，各种复杂的效果，都可以简单的用9-patch实现。

不过也有一些效果不行，看下面这个图：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb9.png "image")](http://freewind.me/wp-content/uploads/2012/11/image9.png)

它无法使用9-patch，因为在竖方向上，它没有办法分成三行。只能横向缩放。

与设计师沟通后，把它变成了这样：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb10.png "image")](http://freewind.me/wp-content/uploads/2012/11/image10.png)

虽然风格变了，没前面的好看，不过这也是效果与开发难度之间平衡的结果。

例子看完了，那么9-patch到底有哪些规定呢？看下图：

 

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb11.png "image")](http://freewind.me/wp-content/uploads/2012/11/image11.png)

 

在一个原始图片的四周，可加上4条黑线。左与上，用来确定图片哪些部分是可缩放的。右与下，表示如果把该图片当作背景，那么里面哪些部区域是可填充的（相当于设了padding）。通常简单的情况，我们只需要画出左与上即可，因为右与下默认使用左与上的设置。

在android中提供了一个专门画9-patch的工具，由swing写成，位于android-sdk/tools/draw9patch.bat。运行后界面如下：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb12.png "image")](http://freewind.me/wp-content/uploads/2012/11/image12.png)

左边是打开的原始图片，右边是实时变形后的预览图，下面是各种选项。

左边的图片，只有四条边是供我们画线的。如果把鼠标移到中间不可编辑的区域，会显示下图：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb13.png "image")](http://freewind.me/wp-content/uploads/2012/11/image13.png)

表示禁止画点。

我们通常要做的，仅仅是在最左边和最上面的一像素宽的区域里，画出一条线，表示可缩放的区域：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb14.png "image")](http://freewind.me/wp-content/uploads/2012/11/image14.png)

看到左与上那两条黑线了吗？画的过程比较痛苦，因为没有快捷键可一下子画好，鼠标歪一下中间就会漏很多点，只能一点点补。所以在制作图片时，要弄的小一点。

想以直观的方式看缩放区域吗？勾选下面的"show patches"，效果如下：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb15.png "image")](http://freewind.me/wp-content/uploads/2012/11/image15.png)

可以看到我上面那条边画得靠左了。从右边的实时预览区域，可看到会有不好看的变形：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb16.png "image")](http://freewind.me/wp-content/uploads/2012/11/image16.png)

这时可按住shift，再用鼠标点去错误的点：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb17.png "image")](http://freewind.me/wp-content/uploads/2012/11/image17.png)

画好后保存，它会以.9.png的格式保存为图片，然后在android中当作普通图片使用即可，android会自动处理缩放时的变形。

在前面的操作过程中，发现画线很不方便，因为经常漏掉一些点，出现像下面这样的图：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb18.png "image")](http://freewind.me/wp-content/uploads/2012/11/image18.png)

这是怎么回事，会不会有问题？android会怎么处理呢？

不用担心，这实际上是超出我预期的一个强大功能：可指定多个缩放区域。正是这个功能，彻底打动了我。

看下面这张图片：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb19.png "image")](http://freewind.me/wp-content/uploads/2012/11/image19.png)

如果我希望在缩放的过程中，数学不变，而它们之间的空白会变，怎么办呢？好办，按下面这种方式画出黑线：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb20.png "image")](http://freewind.me/wp-content/uploads/2012/11/image20.png)

再看变形后的效果：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb21.png "image")](http://freewind.me/wp-content/uploads/2012/11/image21.png)[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb22.png "image")](http://freewind.me/wp-content/uploads/2012/11/image22.png)[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb23.png "image")](http://freewind.me/wp-content/uploads/2012/11/image23.png)

果然数字没变，而空白变了，厉害！

这个功能对于这种情况很有用：

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb24.png "image")](http://freewind.me/wp-content/uploads/2012/11/image24.png)

如果把这个图片当作整体（包含上面的字），想让它适应多种大小的同时，字保持不变，知道怎么做了吧？

这些基本功能已经非常强大了。之前需要在android的布局文件中，繁琐无比地描述、设置，而现在需要做的，仅仅是画几条线。

另外发现除了可以画黑线外，还可以画layout，不过我还不清楚该功能有什么用，需要再研究一下。如果很重要的话，会补上来。

[![image](http://freewind.me/wp-content/uploads/2012/11/image_thumb25.png "image")](http://freewind.me/wp-content/uploads/2012/11/image25.png)

补充资料：

*   [http://radleymarx.com/blog/simple-guide-to-9-patch/](http://radleymarx.com/blog/simple-guide-to-9-patch/)
*   关于前面提到的layout: [http://stackoverflow.com/questions/11406521/android-9-patch-tool-what-is-the-new-layout-bounds-feature](http://stackoverflow.com/questions/11406521/android-9-patch-tool-what-is-the-new-layout-bounds-feature)
