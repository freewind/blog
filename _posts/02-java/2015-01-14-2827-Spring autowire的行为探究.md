---
layout: post
id: 2827
alias: find-the-behavior-of-spring-autowire
tags: spring
date: 2015-01-14 20:39:22
title: Spring autowire的行为探究
---

这几天在做卡时，需要将一个java6程序升级到java8，其中spring的版本也必须跟着升级。在这个过程中，发现了spring的autowire的某些行为，跟我之前的认知不同，所以与同事一起专门研究了一下，记录下来。

autowire="byType"是针对谁
------------------------

首先在同事的提醒下，发现了自己长期以来的一个误解。

在Spring的xml中，我们在定义`bean`的时候，可以给它指定一个`autowire`的属性，其值有多种：`default`, `byType`, `byName`等

```xml
<bean id="userService" class="my.UserService" autowire="byName">
</bean>
```

问题：这个`autowire`与这个`userService` bean之间到底是什么关系？

我一直以为，在`userService`这里加了`autowire="byName"`后，所以其它需要使用`userService`的bean，都会以`byName`的方式来使用它。

然后今天才知道，它是说，如果这个`userService`里引用了别的bean，将会以`byName`的方式去寻找它们！跟我之前理解的正好相反。

可参考<http://docs.spring.io/spring/docs/4.0.0.M1/spring-framework-reference/html/beans.html#beans-standard-annotations>里的一段话：

> byName    
> Autowiring by property name. Spring looks for a bean with the same name as the property that needs to be autowired. For example, if a bean definition is set to autowire by name, and it contains a master property (that is, it has a setMaster(..) method), Spring looks for a bean definition named master, and uses it to set the property.

注意最后一句话。

一个让我无法理解的例子
------------------

下面是代码中一段让我无法理解的例子，为了方便解释，特别简化了一下。

首先看一段java代码，它们都位于`my`包下：

```java
public class User {
    private String name;
    public User(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
}

@Component
public class UserService {

    @Autowired
    private User user1;
    @Autowired
    private User user2;

    public String getNames() {
        return user1.getName() + " & " + user2.getName();
    }

}
```

再看xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
     http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-3.1.xsd">

    <context:component-scan base-package="my"/>

    <context:annotation-config/>

    <bean id="user1" class="my.User">
        <constructor-arg value="Freewind"/>
    </bean>

    <bean id="user2" class="my.User">
        <constructor-arg value="Lily"/>
    </bean>

</beans>
```

然后执行：

```java
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {

    public static void main(String[] args) {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService service = context.getBean(UserService.class);
        System.out.println(service.getNames());
    }
}
```

我以为会报错的，因为在`UserService`里同时`@Autowired`了两个相同类型的bean，而没有指定名字。按照前面文档中的说法，我觉得它是会报错的。

但是它居然正常执行了，并且成功地打印出：

    Freewind & Lily

为什么？

xml中的autowire
---------------

当我看到`@Autowired`的时候，我把它跟xml中的bean属性`autowire`当成了一回事，再加上我以为xml中的`autowire`默认值是`byType`(实际上是`no`，即不自动注入)，所以我觉得它应该会报如“找到多个同类型的bean不知道如何选择”这样的错。

然后后来发现，这两个autowire之间没多大关系。为了方便后面的理解，这这里要先把xml中的`autowire`的行为搞清楚。

由于早期Java不支持注解，所以一开始Spring不支持`@Autowired`，只提供了xml中的写法，并且很完善。前面说的`no/byName/byType/constructor`这样的值，实际上只针对xml中的`autowire`。

如果我们去掉`UserService`类中的`@Autowired`，则这个程序的行为将跟文档中写的一模一样：

1. 如果在xml中不提供autowire，即`autowire="no"`，会则抛NullPointerException
2. 如果写成`autowire="byType"`，会报"符合User类型的bean太多，我不知道如何选择"这样的错
3. 如果写成`autowire="byName"`，会正常运行

所以要清楚，这些值仅针对xml中的定义生效。

@Autowired
----------

然而我们在`UserService`里还使用了`@Autowired`，这样Spring在启动时，将会同时根据xml与代码中的注解信息来组装bean。对于`UserService`中的`user1`和`user2`，它实际上使用了与前面xml中`autowire`不同的行为。

针对前面的例子，它实际上这么做的：

1. 如果某个字段是`@Autowired`，将会去寻找有没有跟它类型匹配的唯一bean。如果有，直接用它
2. 如果发现多个满足条件的，则要看该字段是有没有使用`@Qualifier("user1")`这样的注解说明到底使用哪个名字的bean。如果有，则使用它
3. 如果没有`@Qualifier`，则以字段名为bean的`id`，去寻找合适的bean。这是最后一种尝试的手段，也叫`fallback`

这个答案来自于[stackoverflow上网友的回答](http://stackoverflow.com/a/27937875/342235)，经过我对spring源代码的单步调试，发现基本上是正确的，只是实际过程要比这里写的更复杂一些，多了几个步骤。

这样的话，就可以解释前面的代码为什么可以正常运行了。我想spring为注解提供较为复杂的行为，可能是因为这样使用起来更简单一些，我可不想在Java代码的注解中，写太多东西。

使用@Qualifier
--------------

我觉得对于例子中的情况，我们应该显式使用`@qualifier`来减少可能会误会，让这种引用关系更加明显一些，毕竟这些没有在文档中显式说明的东西，在版本升级时很容易出问题。

所以我最后在项目中，把上面的代码改成：


```
    @Autowired
    @Qualifier("user1")
    private User user1;

    @Autowired
    @Qualifier("user2")
    private User user2;
```

使用@Resource
-------------

同事提醒，还可以使用`@Resource`，更加简化一些。`javax.annotation.Resource`注解是java标准化后提供的注解，让我们不必依赖于spring。它同时拥有`@Autowired`和`@Qualifier`的作用。

所以上面的代码还可以写成：

```
    @Resource(name = "user1")
    private User user1;

    @Resource(name = "user2")
    private User user2;
```


仅有@Autowired，在不同版本中的不确定行为
------------------------------------

在前面的例子中，如果我们既没有加`@Qualifier`也没有用`Resource`，则在不同的spring版本或者同一个版本但用法不同时，也会有不确定的行为。

比如，按照上面的分析，按说只有`@Autowired`，在我们将spring从3.x升级到4.x后，应该不会报错，实际上某个测试在spring 4.x下报错了，而在3.x下正常。

经过我两个小时的调试，我依然不清楚问题到底在哪儿。只是发现，在那个失败的测试中，`UserService`这个类被初始化了两次，第一次生成找到了内部的两个`user`，第二次就报“一个User也找不到”的错。最后只好放弃，加上`@Qualifier`解决了问题。


小结
----

通过这次做卡，以及力所能及的深钻，发现了自己长期以来的一些误解，对spring的注入行为也有了更深入的了解。