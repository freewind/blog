---
layout: post
title: 在scala中，如何创建annotation
tags: Scala
date: 2011-09-13 22:27:00
---

在开发scalaeye的过程中，我想定义一些annotation，让用户以注解方式自定义route。所以，要先创建annotation。

发现scala.annotation包下，有一些已经定义好的annotation，看源代码，形如：

```
class MyAnno extends StaticAnnotation
```

于是我也这样定义了一些annotation，并且加到代码中，编译也能通过。

但是，通过class.getAnnotations或method.getAnnotations时，发现找不到这些annotation的信息。

 <span id="more-189"></span>
<p>@唐说，在scala中定义的annotation，仅仅用于scala的编译。如果我们想在程序中使用，只能先在java下定义annotation，然后在scala中调用。

经过测试，发现只有这种方式可行，所以又定义了一些java下的annotation，如：

```
@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
public @interface any {
    public String value() default "";
}
```

然后在scala中调用，即可用，也可得到，完成了预期的功能。可惜纯scala框架的愿望没了，还是得有java代码才行。
