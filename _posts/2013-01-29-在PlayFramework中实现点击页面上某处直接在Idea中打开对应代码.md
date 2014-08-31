---
layout: post
id: 2044
alias: click-on-an-element-on-page-will-open-the-code-inside-idea
tags: PlayFramework1
date: 2013-01-29 18:25:09
title: 在PlayFramework中实现点击页面上某处直接在Idea中打开对应代码
---

当项目中文件多了以后，定位文件很不方便，特别是在一个长长的HTML中找到某个按钮、链接，能把眼睛看瞎。今天我有一个想法，能不能在页面上点击某个元素后，直接在编辑器中打开相应代码吗？这样的话，我们可以在浏览页面的同时，发现问题随时编辑，大大提高开发效率。

经过几个小时的试验，基本上达到了我的要求，目前可以做到这样：

1.  在Play的模板中某些位置加入一些`#{debug /}`标签，它们将以button的形式显示在最终的html中2.  在浏览器中浏览页面时，默认情况下这些button是不可见的，所以不会破坏页面结构3.  当按下ctrl键时，标签对应的button显示出来4.  点击离想编辑的地方最近的button，将在idea中直接打开对应源文件并定位到对应的#{debug/}处

我最开始希望的是在页面上点击任意元素，都能定位到编辑器中，但看起来似乎难以做到，因为这需要模板引擎在解析模板时，记录下每一个html标签所在的行信息，一般模板引擎都不会这么做。所以只能退而求其次，能定位到哪些点也是好的。毕竟在一个长长的html中，在每个页面块处加上一个#{debug/}，就能方便的定位，还是很值得的。

## Idea的RemoteCall插件

在Idea中有一个RemoteCall插件，可以让我们访问一个url时，Idea能根据传入的信息打开相应的文件。这是很关键的一个插件。

*   [http://plugins.jetbrains.net/plugin?pr=webide&pluginId=6027](http://plugins.jetbrains.net/plugin?pr=webide&pluginId=6027)
*   github上源代码：[https://github.com/Zolotov/RemoteCall](https://github.com/Zolotov/RemoteCall)

如果你使用其它的编辑器，可能不需要这样的插件，因为有的编辑器支持打开特定的url，只要构造一个符合的url即可。另外，Firefox也可以通过设置，当点击某种类型的url时，调用外部编辑器打开。

当我们安装好RemoteCall以后，它会自动监听8091端口，只要我们访问一个这样的url:

[http://localhost:8091/?message=admins/index.html:11:2](http://localhost:8091/?message=admins/index.html:11:2)

它就会寻找最匹配的admins/index.html文件，并定位到11行第2列。其中行与列信息是可选的。

## Play中创建一个debug标签

这个debug标签一定得是FastTag，这样才能拿到行数。幸亏play有这样的功能，否则就没法做了。

    public class MyFastTags extends FastTags {
        public static void _debug(Map<?, ?> args, Closure body, PrintWriter out, GroovyTemplate.ExecutableTemplate template, int fromLine) {
            // e.g. {module:wind_articles}/app/views/wind_articles/Categories/index.html
            String filePath = template.template.getName();
            if (filePath.contains("}")) {
                filePath = StringUtils.substringAfterLast(filePath, "}");
            }
            String content = "<span class='debug debug-hide'>\n"
                             + "<button class='btn btn-warning' data-file='"
                             + filePath
                             + "' data-line='"
                             + fromLine
                             + "'>edit</button>\n"
                             + "</span>";
            out.print(content);
        }
    }

    注意在上面的代码中，我们拿到了文件路径和行数，把它们加在一个button的属性中。

    然后我们可在模板中某些重要位置，写上#{debug /}：

    <td>
    #{debug /}
        <div class="container">
            <div class="btn-group">

    它将会在页面中显示为一个button：

    [![image](http://freewind.me/wp-content/uploads/2013/01/image_thumb169.png "image")](http://freewind.me/wp-content/uploads/2013/01/image167.png)

    点击后，则会自动在Idea中打开相应文件，并定位到相应的#{debug /}处。

    ## #{debug/}的样式

    在通常情况下，我们不希望这个debug按钮显示出来，因为它会破坏我们的页面。所以给它定义了一些css样式，让它默认不可见。

    .debug {
        display: inline-block;
    }
    .debug-hide {
        display: none;
    }

    在生成的button里，它同时使用了这两个css，所以不会显示。

    ## jQuery中的处理

    我们需要做到，当按下ctrl键的时候，把这些按钮显示出来。首先我们需要监听keydown事件，判断是否按下了ctrl键：

    jQuery("body").keydown(function (event) {
        // ctrl
        if (event.which == 17) {
            jQuery("body").find(".debug").toggleClass("debug-hide");
        }
    });

    另外还要给那些按钮们加上onclick事件，根据button属性上的文件和行数信息，拼出正确的url，及使用ajax处理（以防止打开新页面）：

    jQuery("body").find(".debug button").click(function (event) {
        var $button = jQuery(event.target);
        var file = $button.attr("data-file");
        var line = $button.attr("data-line");
        jQuery.get('[http://localhost:8091/?message='](http://localhost:8091/?message=) + file + ":" + line);
    });

这样就大功告成了。下面是截屏，大家看看效果如何。

## 效果

[![live-locating](http://freewind.me/wp-content/uploads/2013/01/live-locating_thumb.gif "live-locating")](http://freewind.me/wp-content/uploads/2013/01/live-locating.gif)

## 其它编辑器及其它框架中怎么做

这里面关键的有两点，一是可以通过某种方式让编辑打开某文件及相应行，二是框架里可以取得行数。

关于第一个，很多轻量级编辑器都支持通过某种特定格式的url直接打开某文件，或者写个插件支持。

关于后者，如果你的web框架支持，则很好，否则的话你可以考虑用另一种方法，不定位到行数，而是让编辑器打开文件后自动查找并定位到某个特殊的唯一的字符串。

这样我们在调用#{debug/}（或类似的自定义标签时），可以手动生成一个uuid作为唯一字符串：

#{debug 'dd10d46c35558468fbf206d8130cce0fd' /}

然后修改编辑器插件，让它找到文件后，能过搜索该字符串找到行数。

很多编辑器都有uuid插件可生成并插入一个uuid，所以这操作起来也不是很麻烦。
