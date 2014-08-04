title: spring mvc，吐个槽
tags:
  - Java
date: 2011-09-13 01:57:59
---

昨天做的这个项目是一个spring mvc下的controller的实现。代码写好后，该写demo了，是一个简单的spring mvc的web程序。因为mvc的结构都差不多，而且我几年前也用过一段时间的spring，所以想想这应该不难，到网上找个例子，把配置文件配好就差不多了。 

<span id="more-108"></span>
<p>结果却花了我两个小时，几乎所有的时间都花在了spring的配置上。spring使用xml作为它的配置文件，而且还在xml中玩出了花。虽然其语法还不是怎么麻烦，但是，在定义各个bean的时候，它里面有很多约定的东西，比如哪个类的bean是一定要配置的，就算没有被人引用；哪个bean要赋给另一个bean的；对于流程中的每一步，都给出了一个堆的可选项，让我不知道选哪个好。
<p>我要做的很简单：定义了一个MultiActionController的controller，它可以处理多个url的请求。我想让spring根据url的不同，自动寻找该controller对应的方法名去处理，这是最常见的一种处理方式了。然而我在网上搜了不少教程，一个个试下去，却总是提示找不到url的映射。
<p>spring的提示也真够简洁，就算我已经把log设置为trace了，它还是只会提示：找不到，找不到，找不到。
<p>好在最后找到了一个例子，作者的需求跟我一样，并且都讲在重点上，我按着他的做法，终于成功。两个小时啊，都花在这里去了。[http://www.blogjava.net/wangqi/archive/2007/12/24/66845.html](http://www.blogjava.net/wangqi/archive/2007/12/24/66845.html)
<p>难怪人们都说spring的配置文件是“xml地狱”，这些xml，既有各种各样的语法，定义bean时还有一些特别的约定，还不能使用编辑器的提示功能（比如设置某个bean的属性），让人一看，心里就产生一种畏惧感。这些xml，写错一个地方，程序就跑不起来，而且提示信息可能还没什么用，只能靠长期犯错积累的经验来解决。
<p>另外，把做这个项目的感觉与用playframework做网站的感觉相比，playframework的开发效率真是高了三五倍不止啊。
<p>再另外，spring大力提倡的IoC思想，对于测试来说，真的是非常方便。配合上mockito这个好用的mock库，写起单元测试，基本上没有遇到什么困难。看来我以后得好好研究一下另一个IoC框架，guice，它可不需要像spring这样，满天的xml配置。