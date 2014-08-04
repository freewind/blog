title: BeanKeeper – 简单到了诡异的Orm
tags:
  - Java
date: 2011-09-13 01:46:00
---

[http://beankeeper.netmind.hu/](http://beankeeper.netmind.hu/)

这是另一个ORM，文章写得不太好看，但是内容还是挺让人震惊的。代码太简单了，简单到了诡异的地步。

    public class Book { private String id; private String author; private String title; // getters and setters } 

    要把它的一个实例保存到数据库，只需要:

    ````store.save(new Book());

    删除：

    ````store.delete(book);

    什么映射之类的都不用写，就直接搞定了，连一对多关系的都可以处理。

    <span id="more-96"></span>

    真是如其名：Bean Keeper，只需要写一个Bean，其它全部搞定。

    再看看查询：

    ````List books = store.find("find book");
    ````List books = store.find("find book where title='Java for dummies'");

注意这里返回的这个List是一个自定义的实现，实现了lasy loading。它里面最初只包含了30个左右的对象，如果你用到更多的，它会在需要的时候再查询数据库。所以你可以尽情的查询，不必担心内存不够－－你以为你拿到了全部，实际上只有几十个，但是对于你来说，没什么区别。就像我们的邮箱，看着几G，挺爽，实际上你用到的只有那么一点。

简单的看完了它的介绍，觉得它虽然有创意，我却不是很推荐它。因为它看起来太简单了，简单到了诡异的地步。看着它的代码，我都想不出来它是怎么实现的。在这种简单的表面之下，应该对应着复杂的封装。其结果就是，简单的应用，用起来很爽，但是一旦出问题，只能干瞪眼，想调试都无处下手。因为你不知道它内部是如何实现的，如果简单好理解还好，否则的话，那就祈祷吧。

与它相比，前面那个ActiveObjects还是要清晰得多。