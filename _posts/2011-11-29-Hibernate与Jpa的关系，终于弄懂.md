---
layout: post
title: Hibernate与Jpa的关系，终于弄懂
tags:
  - Java
date: 2011-11-29 15:46:14
---

我知道Jpa是一种规范，而Hibernate是它的一种实现。除了Hibernate，还有EclipseLink(曾经的toplink)，OpenJPA等可供选择，所以使用Jpa的一个好处是，可以更换实现而不必改动太多代码。

在play中定义Model时，使用的是jpa的annotations，比如javax.persistence.Entity, Table, Column, OneToMany等等。但它们提供的功能基础，有时候想定义的更细一些，难免会用到Hibernate本身的annotation。我当时想，jpa这么弱还要用它干什么，为什么不直接使用hibernate的？反正我又不会换成别的实现。

因为我很快决定不再使用hibernate，这个问题就一直放下了。直到我现在在新公司，做项目要用到Hibernate。

<span id="more-588"></span>

我想抛开jpa，直接使用hibernate的注解来定义Model，很快发现了几个问题：

1.  jpa中有Entity, Table，hibernate中也有，但是内容不同
2.  jpa中有Column,OneToMany等，Hibernate中没有，也没有替代品

我原以为hibernate对jpa的支持，是另提供了一套专用于jpa的注解，但现在看起来似乎不是。一些重要的注解如Column, OneToMany等，hibernate没有提供，这说明jpa的注解已经是hibernate的核心，hibernate只提供了一些补充，而不是两套注解。要是这样，hibernate对jpa的支持还真够足量，我们要使用hibernate注解就必定要使用jpa。

实际情况是不是这样？在被群里(Scala交流群132569382)的朋友鄙视一番却没有给出满意答案的时候，我又想起了万能的stackoverflow，上去提了两个问：

1.  [http://stackoverflow.com/questions/8306742/if-i-want-to-use-hibernate-with-annotation-do-i-have-to-use-javax-persistence](http://stackoverflow.com/questions/8306742/if-i-want-to-use-hibernate-with-annotation-do-i-have-to-use-javax-persistence)
2.  [http://stackoverflow.com/questions/8306793/why-jpa-and-hibernate-both-have-entity-and-table-annotations](http://stackoverflow.com/questions/8306793/why-jpa-and-hibernate-both-have-entity-and-table-annotations)

第一个是问如果想用hibernate注解，是不是一定会用到jpa的。网友的回答：“是。如果hibernate认为jpa的注解够用，就直接用。否则会弄一个自己的出来作为补充”

第二个是问，jpa和hibernate都提供了Entity，我们应该用哪个，还是说可以两个一起用？网友回答说“Hibernate的Entity是继承了jpa的，所以如果觉得jpa的不够用，直接使用hibernate的即可”。