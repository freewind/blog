---
layout: post
title: Jump to Javascript world (1)
tags:
  - JavaScript
date: 2012-04-28 17:15:51
---

Web开发真是日新月异，不断有新东西出来，js时代快到来了。

在了解了angularjs的强大与灵活之后，我对平时勤学苦练的java web框架突然有种难以言说的失落，因为我发现，这两个世界在web开发方面有着难以逾越的鸿沟。

就算java在后台如何稳定高效，但对前台都有些无可奈何，必须借助一些js库（如jquery等）来才能增强表现力。然而这前后台之间的配合却是很难如人意：后台开发，如同一台巨型钢铁机器，坚固稳定，但却笨重。前台用jquery，却如同打补丁一样，把一块块破布缝在一起。如果是简单的动态（如局部刷新等），还能勉励撑过去，稍复杂的就会扯出一个个大口子，只能再找一块更大的破布往上缝。

我曾经想使用play+jquery来做一个todolist，折腾很久都没弄出来。后来发现了backbone，眼前一亮，但还是稍嫌复杂。

最后发现了angularjs，原来可以这么轻松。视频为证：[http://www.youtube.com/watch?v=WuiHuZq_cg4&amp;feature=related](http://www.youtube.com/watch?v=WuiHuZq_cg4&amp;feature=related "http://www.youtube.com/watch?v=WuiHuZq_cg4&amp;feature=related")

我本来还是舍不得离开熟悉的java平台，前台angularjs，后台java，通过restful api交互，岂不也是很好？

可惜的是，发现play的route与angularjs的$resource之间有点不太匹配，虽然可以设计成restful api，但play那边仅能通过uri+http method来转向action，把querystring和header之类都忽略了，而这两者常被js前端框架使用。我在设计angularjs与前台交互的过程中，费尽脑筋，都觉得相当别扭。

最后心一横，干脆后台也用js(node.js)，数据库搞个document based的key-value，数据传输全部直接json，省了多少事。

browser <==> web <==> database

没错，全部json，全部js，世界和平统一。

Let’s jump to the javascript world ~