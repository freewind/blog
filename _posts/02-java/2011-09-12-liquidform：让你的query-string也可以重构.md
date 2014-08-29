---
layout: post
title: liquidform：让你的query string也可以重构
tags: Java
date: 2011-09-12 23:47:00
---

今天在google code上看到了一个有趣的项目：liquidform。

它是为了解决这个问题：

我们在写sql的时候，不可避免的要调用一些pojo的字段名，这些字段名的背后，都对应着数据表中的一些字段。有时候，我们会修改pojo中的某些字段，在IDE的强大重构功能下，所有调用这个字段（或者它的getter)的代码都会被自动修改，除了，在sql中用到的名字。这样，bug就出来了，我们只能一个个去找，一个个去改。有没有办法让这些sql也能自动更改呢？

看看liquidform是怎么做的。

原代码：

```

   @Entity
   public class Person {
   private String firstName;
   private String surname;
   public String getFirstName() { return firstName; }
   public String getSurname() { return surname; }
   // setters omitted
}
```

List people = em.createQuery( &#8220;SELECT FROM Person p WHERE p.surname LIKE 'Smith%'&#8221;) .getResultList();

变为：

```
Person p = LiquidForm.use(Person.class, "p");
   List people = em.createQuery(
   select(p).from(Person.class).as(p).where(like(p.getSurname(), "Smith%")).toString())
   .getResultList();

```

魔术就在，通过LiquidForm#use方法，给pojo包上了一层。当使用那一连串函数模拟sql语句的时候，表面上调用的是pojo的函数，实际上在内部记录了其它的信息。这样，我们既可以利用到IDE的重构功能，又能保证得到正确的sql。

先不论它的实用性到底有多少，这个创意，的确很不错。
