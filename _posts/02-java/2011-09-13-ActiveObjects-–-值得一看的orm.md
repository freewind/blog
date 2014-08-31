---
layout: post
id: 95
alias: activeobjects-an-orm-worth-noting
tags: Java
date: 2011-09-13 01:45:23
title: ActiveObjects – 值得一看的orm
---

[http://www.javalobby.org/articles/activeobjects](http://www.javalobby.org/articles/activeobjects)

这篇文章，讲述了一个故事，悬念重重，引人入胜，看得非常过瘾。技术文章写成这样，作者可真是厉害。

他一步步的讲述了现在的ORM有哪些问题，RoR的ActiveRecord有哪些优点可以借鉴。同时因为java语言本身的限制，无法做到和ActionRecord一模一样，但是可以根据java中的一些语言特点，采用迂回战术，做到八分满意。于是，ActiveObjects诞生了。

可以说这篇文章看完后，基本上就知道ActiveObjects怎么用了。与Hibernate等相比，真的是相当的简单，半小时内应该就可以掌握。我下载了它的包看了一下，里面的包和类都比较少，很简洁。有空的时候，一定要好好研究一下。<span id="more-95"></span>

[![1](http://freewind.me/wp-content/uploads/2011/09/1_thumb6.jpg "1")](http://freewind.me/wp-content/uploads/2011/09/16.jpg)

项目地址：

[https://activeobjects.dev.java.net/](https://activeobjects.dev.java.net/)

今天又仔细地想了想，觉得ActiveObjects中有一个地方，还是让人用起来不太舒服。看这里：

// Person.java

public interface Person extends Entity {

public String getName();

public void setName(String name);

public int getAge();

public void setAge(int age);

@SQLType(Types.CLOB)

public String getComment();

@SQLType(Types.CLOB)

public void setComment(String comment);

public Family getFamily();

public void setFamily();

@ManyToMany(PersonToPerson.class)

public Person[] getPeople();

}

定义一个实体的时候，用的是interface，这里倒好理解和接受，关键是，它用的是getter/setter来对应数据库中的字段。这个写起来会比较麻烦，因为通常我们都是利用编辑器来自动生成的，快速且不易出错。而现在全得手动去写，错一个字母到时候查错就很麻烦。同时因为它是在一个接口里，就算我们先利用编辑器生成getter/setter，还是得把每个方法体的括号什么的都删掉，这也挺麻烦的。

我想ActiveObjects为什么不在接口里定义一些常量，比如：

public interface Person extends Entity {

public String name;

}

调用者直接使用Person.name = &#8220;sss&#8221;这样的方式来赋值，ActiveObjects在后台把它变为getter/setter呢？结果一尝试，发现这样是行不通的，因为接口中的常量都是final，无法再赋值，所以ActiveObjects只好使用getter/setter来做。

我觉得如果不用接口而用类来做，就可以实现只使用字段不用getter/setter，可以简化很多，像play中就是这么做的。可是这样需要使用cglib的库去动态改变字节码，而ActiveObjects强调它们不想使用这样的方式，所以只好用接口。

如果你能接受这样稍稍的麻烦，ActiveObjects还是一个很不错的ORM框架，值得尝试。如果不喜欢这样的方式，可以再看看这个叫Siena的东西。
