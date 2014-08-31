---
layout: post
id: 1367
alias: modify-wintycodearea-plugin-of-livewrite-to-insert-code-block
tags: Windows
date: 2013-01-01 20:30:32
title: 修改LiveWriter插件WintyCodeArea，生成google-code-prettify适用的代码块
---

## 开源就是好！

一直觉得LiveWriter中插入源代码不方便，使用了几个插件都不好用，因为基本上向代码中插入很多style，嵌入到文档中，无法与google-code-prettify很好的结合起来。

今天看到了一个WintyCodeArea的插件：[http://www.blogjava.net/wintys/archive/2009/10/05/dotNET_WindowLiveWriter_WintyCodeArea.html](http://www.blogjava.net/wintys/archive/2009/10/05/dotNET_WindowLiveWriter_WintyCodeArea.html)

它的功能比较简单，可以把选中的代码外面包一个`<div class="mycode">...</div>`，虽然不太符合我的需求，但是由于作者开放了源代码，我照猫画虎，做出了自己想要的功能。

源代码我已经发布在github上：[https://github.com/freewind/WintyCodeArea](https://github.com/freewind/WintyCodeArea)，感谢原作者的代码。因为有原作者的劳动成果，我才能在C#还未入门的情况下，做出自己需要的效果。

所做的主要修改在WintyCodeArea类中，这一段：

<div class="mycode">

    if (m_settings.RemoveBr)
          content += originalContent
          .Replace("<P>", "").Replace("</P>", "")
          .Replace("\r\n", "").Replace("\n", "")
          .Replace("<BR>", "\n")
          .Replace("&nbsp;", " ");
    </div>

    我增加了一个配置选项：`RemoveBr`，用来表示是否去除代码中的`<br>`标签。

    当我们把代码直接拷贝到live writer后，live writer为了正确显示其格式，会把换行符`\n`变为`<BR>`，同时把多于一个的空格换成`&nbsp;`。如果我们直接在它们外面包上`<pre><code>`，显然是不行的，因为google-code-prettify需要的是纯文本格式的代码，里面不能含有html代码。我们需要在代码中，把`<BR>`换成换行符`\n`，把`&nbsp;`换成空格。

    另外，我们得到的`content`字符串（即选中的内容）会莫名多出一些换行符，必须先把它们去掉，不然最后生成的代码，格式是乱的。

    另外，在选择时，有可能把代码块外面包着的`<p>`也选中，不需要它，所以把它也去掉。

    这样对选中代码进行处理后，再在外面套上`<pre><code>`，就是一个满足google-code-prettify要求的代码块了。

    ## 配置Visual Studio

    这里记录一下Visual Studio的操作，以免以后需要的时候忘了。其实四个月前我还学习一段时间的C#和VS，基本算入门了，但四个月之后，已经记不清了，花了不少时间去配置VS，才让项目跑起来。

    我用的是Visual Studio 2012，系统是win7 x64.

    添加LiveWriter的dll

    源代码项目中没有包含live writer的dll，我们必须先安装好live writer，然后将它目录下的WindowsLive.Writer.Api.dll引入到项目中。该dll就是让人们写live writer插件用的，里面提供了一些类可供使用。

    [![image](http://freewind.me/wp-content/uploads/2013/01/image29.png "image")](http://freewind.me/wp-content/uploads/2013/01/image29.png)

    在浏览页，选择live writer中的WindowsLive.Writer.Api.dll，确定即可，此时如图：

    [![image](http://freewind.me/wp-content/uploads/2013/01/image30.png "image")](http://freewind.me/wp-content/uploads/2013/01/image30.png)

    添加之后，按F6，重新生成一下解决方案。

    如果你是x64的系统，此时可能会提示错误，因为live writer是32位，我们必须手动把项目的编译类型改为x86。

    右键点“解决方案”－“配置属性”－“配置”，右上角“配置管理器”。

    出来如下的对话框，点“活动解决方案平台”，选“新建...”

    [![image](http://freewind.me/wp-content/uploads/2013/01/image31.png "image")](http://freewind.me/wp-content/uploads/2013/01/image31.png)

    弹出如下的对话框，选择x86:

    [![image](http://freewind.me/wp-content/uploads/2013/01/image32.png "image")](http://freewind.me/wp-content/uploads/2013/01/image32.png)

    确定后，回到前面的页面，确定这里是x86:

    [![image](http://freewind.me/wp-content/uploads/2013/01/image33.png "image")](http://freewind.me/wp-content/uploads/2013/01/image33.png)

    然后再重新生成解决方案，如果没有提示代码错误，表明环境和代码都正确。

    此时可能会出现这样的一个错误：

    [![image](http://freewind.me/wp-content/uploads/2013/01/image34.png "image")](http://freewind.me/wp-content/uploads/2013/01/image34.png)

    这是说把生成的WintyCodeArea.dll拷贝到Live writer的Plugins目录下出错。如果出了这个错，可以手动把WintyCodeArea.dll拷贝过去。

    这个WintyCodeArea.dll就是这个项目生成的库文件，直接把它放在Live writer的Plugins目录下，再重新打开Live writer即可。如果没有错误，你可以在其插件列表中看到WintyCodeArea这个插件名：

    [![image](http://freewind.me/wp-content/uploads/2013/01/image35.png "image")](http://freewind.me/wp-content/uploads/2013/01/image35.png)

    ## 配置WintyCodeArea

    确定如下：

    [![image](http://freewind.me/wp-content/uploads/2013/01/image36.png "image")](http://freewind.me/wp-content/uploads/2013/01/image36.png)

    ## 将插件添加到“快速访问工具栏”

    因为live writer不支持自定义快捷键，我也没有找到在插件中设置快捷键的办法，每次使用时都需要切换工具面板，十分不便。所以把加到“快速访问工具栏”中，使用方便一些。

    [![image](http://freewind.me/wp-content/uploads/2013/01/image37.png "image")](http://freewind.me/wp-content/uploads/2013/01/image37.png)

    以后使用时，只需要点这里就行了。

    [![image](http://freewind.me/wp-content/uploads/2013/01/image38.png "image")](http://freewind.me/wp-content/uploads/2013/01/image38.png)

    这样live writer这块就搞定了。

    ## 博客端

    由于wordpress不支持google-code-prettify，所以我们还需要修改wordpress代码，让它可以给代码块着色。

    首先到[http://code.google.com/p/google-code-prettify/](http://code.google.com/p/google-code-prettify/)下载所需要的js文件，拿到prettify.min.js文件，加到博客的header中。

    然后在页面的最下面加上：

    <div class="mycode">
    <script type="text/javascript">
    $(function() {
      $("pre").addClass("prettyprint");
      prettyPrint();
    });
    </script>
    </div>

    它会给所有的<pre>标签加上prettyprint这个css class供google-code-prettify定位，然后调用google-code-prettify中的方法prettyPrint，给代码块着色。

    再设置css:

    <div class="mycode">
    pre {
        margin: 0px 0px 20px;
        padding: 10px;
        border: 1px solid #CCC;
        _height: 1%;
        white-space: pre-wrap;
        display: block;
        font-size: 12.025px;
        line-height: 18px;
        word-break: break-all;
        word-wrap: break-word;
        white-space: pre-wrap;
        background-color: whiteSmoke;
        border: 1px solid rgba(0, 0, 0, 0.15);
        -webkit-border-radius: 4px;
        -moz-border-radius: 4px;
        border-radius: 4px;
    }pre code {
        color: #333;
        font-size: 14px;
        border: 0px;
        background-color: transparent;
        margin: 0px;
        padding: 0px;
    }code {
        font-family: Consolas, Menlo, Monaco, Lucida Console, Liberation Mono, DejaVu Sans Mono, Bitstream Vera Sans Mono, Courier New, monospace, serif;
        background-color: #eeeeee;
        border: 1px solid rgba(0, 0, 0, 0.15);
        -webkit-border-radius: 4px;
        -moz-border-radius: 4px;
        border-radius: 4px;
        color: #871b15;
        margin: 0px 3px;
        padding: 3px 6px;
    }/* prettify */
    .com {
        color: #93a1a1;
    }.lit {
        color: #195f91;
    }.pun, .opn, .clo {
        color: #93a1a1;
    }.fun {
        color: #dc322f;
    }.str, .atv {
        color: #D14;
    }.kwd, .linenums .tag {
        color: #1e347b;
    }.typ, .atn, .dec, .var {
        color: teal;
    }.pln {
        color: #48484c;
    }

</div>

这样，代码就可以显示出这样的效果：

[![image](http://freewind.me/wp-content/uploads/2013/01/image39.png "image")](http://freewind.me/wp-content/uploads/2013/01/image39.png)
