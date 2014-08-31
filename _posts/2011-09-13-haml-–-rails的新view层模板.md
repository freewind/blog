---
layout: post
alias: haml-rails-new-view-template-engine
tags: 未分类
date: 2011-09-13 01:54:00
title: haml – rails的新view层模板
---

[http://haml-lang.com/](http://haml-lang.com/)

表现层是一个web框架中最重要，却又最被忽视的一层。人们把精力都放在了MC，后台部分已经很完善了，但是前台V，即表现层，却一直没有太多改进。对于表现层，人们总希望&#8221;页面中不要嵌入逻辑代码，内容要与样式分离&#8221;，但是由于难度大，或者后台语言的限制，这个目标很难达到。可以说，我之前见过用过的大多数表面层，做的都不太好，如jsp, asp, php, velocity, freemarker, rails erb等等。

而haml的出现，则让我有了一点惊喜：内容跟样式分离得如此彻底，代码却又如此清晰。虽然对于纯美工MM来说，不是个好消息（完全无法使用dreamweaver等工具），但对于程序员来说，却是非常的贴心。 
<span id="more-104"></span>

初看下，这种模板语言有点怪异，没有出现一个html tag，写起来好像在写配置文件。但是很快就会发现，没有了众多html tag的打扰，可以如此专注的把精力放在内容上，完全不用考虑样式，的确可以让开发效率大大提高。内容全部交给haml，样式全部交给css，各司其职。这种大胆前卫的设计，真的是非常具有想像力。

贴段代码：

```
#profile
.left.column
#date= print_date
#address= current_user.address
.right.column
#email= current_user.email
#bio= current_user.bio

```

它将被解析为：

```
<div id="profile">
<div class="left column">
<div id="date">${print_date}</div>
<div id="address">${current_user.address}</div>
</div>
<div class="right column">
<div id="email">${current_user.emil}</div>
<div id="bio">${current_user.bio}</div>
</div>
</div>
```

对比上下两段，是否觉得前面的代码更让人把注意力集中在内容上？
