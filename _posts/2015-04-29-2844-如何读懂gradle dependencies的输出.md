---
layout: post
id: 2844
alias: how-to-understand-the-output-of-gradle-dependencies
tags: gradle dependencies
date: 2015-04-29 14:00:08
title: 如何读懂gradle dependencies的输出
---

第三方依赖的管理与冲突解决，是每一个项目和构建工具都需要考虑的事情。

在gradle项目中，我们可以声明所使用的依赖，还可以使用它提供的`dependencies`命令查看依赖。这里我们主要关注如何读懂它的输出。

需要注意的是，直接使用`dependencies`只会显式根目录的依赖。如果有多个子目录，则需要加上项目名，如`:core:dependencies`。

示例文件
=======

假设我们创建了一个gradle项目，它只有一个`build.gradle`文件，内容如下：

```
apply plugin: 'java'

repositories.jcenter()

dependencies {
    compile "org.springframework.boot:spring-boot-starter-web:1.1.5.RELEASE"
    compile 'org.slf4j:slf4j-api:1.7.1'
}
```

gradle dependencies
===================

我们运行`gradle dependencies`后，则显示如下：

```
➜  gradle-dependency-insight git:(master) ✗ gradle dependencies
:dependencies

------------------------------------------------------------
Root project
------------------------------------------------------------

archives - Configuration for archive artifacts.
No dependencies

compile - Compile classpath for source set 'main'.
+--- org.springframework.boot:spring-boot-starter-web:1.1.5.RELEASE
|    +--- org.springframework.boot:spring-boot-starter:1.1.5.RELEASE
|    |    +--- org.springframework.boot:spring-boot:1.1.5.RELEASE
|    |    |    +--- org.springframework:spring-core:4.0.6.RELEASE
|    |    |    |    \--- commons-logging:commons-logging:1.1.3
|    |    |    \--- org.springframework:spring-context:4.0.6.RELEASE
|    |    |         +--- org.springframework:spring-aop:4.0.6.RELEASE
|    |    |         |    +--- aopalliance:aopalliance:1.0
|    |    |         |    +--- org.springframework:spring-beans:4.0.6.RELEASE
|    |    |         |    |    \--- org.springframework:spring-core:4.0.6.RELEAS (*)
|    |    |         |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         \--- org.springframework:spring-expression:4.0.6.RELEASE
|    |    |              \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-autoconfigure:1.1.5.RELEASE
|    |    |    \--- org.springframework.boot:spring-boot:1.1.5.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-starter-logging:1.1.5.RELEASE
|    |    |    +--- org.slf4j:jcl-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:jul-to-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:log4j-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    \--- ch.qos.logback:logback-classic:1.1.2
|    |    |         +--- ch.qos.logback:logback-core:1.1.2
|    |    |         \--- org.slf4j:slf4j-api:1.7.6 -> 1.7.7
|    |    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    \--- org.yaml:snakeyaml:1.13
|    +--- org.springframework.boot:spring-boot-starter-tomcat:1.1.5.RELEASE
|    |    +--- org.apache.tomcat.embed:tomcat-embed-core:7.0.54
|    |    +--- org.apache.tomcat.embed:tomcat-embed-el:7.0.54
|    |    \--- org.apache.tomcat.embed:tomcat-embed-logging-juli:7.0.54
|    +--- com.fasterxml.jackson.core:jackson-databind:2.3.3
|    |    +--- com.fasterxml.jackson.core:jackson-annotations:2.3.0
|    |    \--- com.fasterxml.jackson.core:jackson-core:2.3.3
|    +--- org.hibernate:hibernate-validator:5.0.3.Final
|    |    +--- javax.validation:validation-api:1.1.0.Final
|    |    +--- org.jboss.logging:jboss-logging:3.1.1.GA
|    |    \--- com.fasterxml:classmate:1.0.0
|    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    +--- org.springframework:spring-web:4.0.6.RELEASE
|    |    +--- org.springframework:spring-aop:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|    |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    \--- org.springframework:spring-webmvc:4.0.6.RELEASE
|         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-expression:4.0.6.RELEASE (*)
|         \--- org.springframework:spring-web:4.0.6.RELEASE (*)
\--- org.slf4j:slf4j-api:1.7.1 -> 1.7.7

default - Configuration for default artifacts.
+--- org.springframework.boot:spring-boot-starter-web:1.1.5.RELEASE
|    +--- org.springframework.boot:spring-boot-starter:1.1.5.RELEASE
|    |    +--- org.springframework.boot:spring-boot:1.1.5.RELEASE
|    |    |    +--- org.springframework:spring-core:4.0.6.RELEASE
|    |    |    |    \--- commons-logging:commons-logging:1.1.3
|    |    |    \--- org.springframework:spring-context:4.0.6.RELEASE
|    |    |         +--- org.springframework:spring-aop:4.0.6.RELEASE
|    |    |         |    +--- aopalliance:aopalliance:1.0
|    |    |         |    +--- org.springframework:spring-beans:4.0.6.RELEASE
|    |    |         |    |    \--- org.springframework:spring-core:4.0.6.RELEAS (*)
|    |    |         |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         \--- org.springframework:spring-expression:4.0.6.RELEASE
|    |    |              \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-autoconfigure:1.1.5.RELEASE
|    |    |    \--- org.springframework.boot:spring-boot:1.1.5.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-starter-logging:1.1.5.RELEASE
|    |    |    +--- org.slf4j:jcl-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:jul-to-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:log4j-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    \--- ch.qos.logback:logback-classic:1.1.2
|    |    |         +--- ch.qos.logback:logback-core:1.1.2
|    |    |         \--- org.slf4j:slf4j-api:1.7.6 -> 1.7.7
|    |    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    \--- org.yaml:snakeyaml:1.13
|    +--- org.springframework.boot:spring-boot-starter-tomcat:1.1.5.RELEASE
|    |    +--- org.apache.tomcat.embed:tomcat-embed-core:7.0.54
|    |    +--- org.apache.tomcat.embed:tomcat-embed-el:7.0.54
|    |    \--- org.apache.tomcat.embed:tomcat-embed-logging-juli:7.0.54
|    +--- com.fasterxml.jackson.core:jackson-databind:2.3.3
|    |    +--- com.fasterxml.jackson.core:jackson-annotations:2.3.0
|    |    \--- com.fasterxml.jackson.core:jackson-core:2.3.3
|    +--- org.hibernate:hibernate-validator:5.0.3.Final
|    |    +--- javax.validation:validation-api:1.1.0.Final
|    |    +--- org.jboss.logging:jboss-logging:3.1.1.GA
|    |    \--- com.fasterxml:classmate:1.0.0
|    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    +--- org.springframework:spring-web:4.0.6.RELEASE
|    |    +--- org.springframework:spring-aop:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|    |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    \--- org.springframework:spring-webmvc:4.0.6.RELEASE
|         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-expression:4.0.6.RELEASE (*)
|         \--- org.springframework:spring-web:4.0.6.RELEASE (*)
\--- org.slf4j:slf4j-api:1.7.1 -> 1.7.7

runtime - Runtime classpath for source set 'main'.
+--- org.springframework.boot:spring-boot-starter-web:1.1.5.RELEASE
|    +--- org.springframework.boot:spring-boot-starter:1.1.5.RELEASE
|    |    +--- org.springframework.boot:spring-boot:1.1.5.RELEASE
|    |    |    +--- org.springframework:spring-core:4.0.6.RELEASE
|    |    |    |    \--- commons-logging:commons-logging:1.1.3
|    |    |    \--- org.springframework:spring-context:4.0.6.RELEASE
|    |    |         +--- org.springframework:spring-aop:4.0.6.RELEASE
|    |    |         |    +--- aopalliance:aopalliance:1.0
|    |    |         |    +--- org.springframework:spring-beans:4.0.6.RELEASE
|    |    |         |    |    \--- org.springframework:spring-core:4.0.6.RELEAS (*)
|    |    |         |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         \--- org.springframework:spring-expression:4.0.6.RELEASE
|    |    |              \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-autoconfigure:1.1.5.RELEASE
|    |    |    \--- org.springframework.boot:spring-boot:1.1.5.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-starter-logging:1.1.5.RELEASE
|    |    |    +--- org.slf4j:jcl-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:jul-to-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:log4j-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    \--- ch.qos.logback:logback-classic:1.1.2
|    |    |         +--- ch.qos.logback:logback-core:1.1.2
|    |    |         \--- org.slf4j:slf4j-api:1.7.6 -> 1.7.7
|    |    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    \--- org.yaml:snakeyaml:1.13
|    +--- org.springframework.boot:spring-boot-starter-tomcat:1.1.5.RELEASE
|    |    +--- org.apache.tomcat.embed:tomcat-embed-core:7.0.54
|    |    +--- org.apache.tomcat.embed:tomcat-embed-el:7.0.54
|    |    \--- org.apache.tomcat.embed:tomcat-embed-logging-juli:7.0.54
|    +--- com.fasterxml.jackson.core:jackson-databind:2.3.3
|    |    +--- com.fasterxml.jackson.core:jackson-annotations:2.3.0
|    |    \--- com.fasterxml.jackson.core:jackson-core:2.3.3
|    +--- org.hibernate:hibernate-validator:5.0.3.Final
|    |    +--- javax.validation:validation-api:1.1.0.Final
|    |    +--- org.jboss.logging:jboss-logging:3.1.1.GA
|    |    \--- com.fasterxml:classmate:1.0.0
|    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    +--- org.springframework:spring-web:4.0.6.RELEASE
|    |    +--- org.springframework:spring-aop:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|    |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    \--- org.springframework:spring-webmvc:4.0.6.RELEASE
|         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-expression:4.0.6.RELEASE (*)
|         \--- org.springframework:spring-web:4.0.6.RELEASE (*)
\--- org.slf4j:slf4j-api:1.7.1 -> 1.7.7

testCompile - Compile classpath for source set 'test'.
+--- org.springframework.boot:spring-boot-starter-web:1.1.5.RELEASE
|    +--- org.springframework.boot:spring-boot-starter:1.1.5.RELEASE
|    |    +--- org.springframework.boot:spring-boot:1.1.5.RELEASE
|    |    |    +--- org.springframework:spring-core:4.0.6.RELEASE
|    |    |    |    \--- commons-logging:commons-logging:1.1.3
|    |    |    \--- org.springframework:spring-context:4.0.6.RELEASE
|    |    |         +--- org.springframework:spring-aop:4.0.6.RELEASE
|    |    |         |    +--- aopalliance:aopalliance:1.0
|    |    |         |    +--- org.springframework:spring-beans:4.0.6.RELEASE
|    |    |         |    |    \--- org.springframework:spring-core:4.0.6.RELEAS (*)
|    |    |         |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         \--- org.springframework:spring-expression:4.0.6.RELEASE
|    |    |              \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-autoconfigure:1.1.5.RELEASE
|    |    |    \--- org.springframework.boot:spring-boot:1.1.5.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-starter-logging:1.1.5.RELEASE
|    |    |    +--- org.slf4j:jcl-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:jul-to-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:log4j-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    \--- ch.qos.logback:logback-classic:1.1.2
|    |    |         +--- ch.qos.logback:logback-core:1.1.2
|    |    |         \--- org.slf4j:slf4j-api:1.7.6 -> 1.7.7
|    |    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    \--- org.yaml:snakeyaml:1.13
|    +--- org.springframework.boot:spring-boot-starter-tomcat:1.1.5.RELEASE
|    |    +--- org.apache.tomcat.embed:tomcat-embed-core:7.0.54
|    |    +--- org.apache.tomcat.embed:tomcat-embed-el:7.0.54
|    |    \--- org.apache.tomcat.embed:tomcat-embed-logging-juli:7.0.54
|    +--- com.fasterxml.jackson.core:jackson-databind:2.3.3
|    |    +--- com.fasterxml.jackson.core:jackson-annotations:2.3.0
|    |    \--- com.fasterxml.jackson.core:jackson-core:2.3.3
|    +--- org.hibernate:hibernate-validator:5.0.3.Final
|    |    +--- javax.validation:validation-api:1.1.0.Final
|    |    +--- org.jboss.logging:jboss-logging:3.1.1.GA
|    |    \--- com.fasterxml:classmate:1.0.0
|    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    +--- org.springframework:spring-web:4.0.6.RELEASE
|    |    +--- org.springframework:spring-aop:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|    |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    \--- org.springframework:spring-webmvc:4.0.6.RELEASE
|         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-expression:4.0.6.RELEASE (*)
|         \--- org.springframework:spring-web:4.0.6.RELEASE (*)
\--- org.slf4j:slf4j-api:1.7.1 -> 1.7.7

testRuntime - Runtime classpath for source set 'test'.
+--- org.springframework.boot:spring-boot-starter-web:1.1.5.RELEASE
|    +--- org.springframework.boot:spring-boot-starter:1.1.5.RELEASE
|    |    +--- org.springframework.boot:spring-boot:1.1.5.RELEASE
|    |    |    +--- org.springframework:spring-core:4.0.6.RELEASE
|    |    |    |    \--- commons-logging:commons-logging:1.1.3
|    |    |    \--- org.springframework:spring-context:4.0.6.RELEASE
|    |    |         +--- org.springframework:spring-aop:4.0.6.RELEASE
|    |    |         |    +--- aopalliance:aopalliance:1.0
|    |    |         |    +--- org.springframework:spring-beans:4.0.6.RELEASE
|    |    |         |    |    \--- org.springframework:spring-core:4.0.6.RELEAS (*)
|    |    |         |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         \--- org.springframework:spring-expression:4.0.6.RELEASE
|    |    |              \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-autoconfigure:1.1.5.RELEASE
|    |    |    \--- org.springframework.boot:spring-boot:1.1.5.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-starter-logging:1.1.5.RELEASE
|    |    |    +--- org.slf4j:jcl-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:jul-to-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:log4j-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    \--- ch.qos.logback:logback-classic:1.1.2
|    |    |         +--- ch.qos.logback:logback-core:1.1.2
|    |    |         \--- org.slf4j:slf4j-api:1.7.6 -> 1.7.7
|    |    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    \--- org.yaml:snakeyaml:1.13
|    +--- org.springframework.boot:spring-boot-starter-tomcat:1.1.5.RELEASE
|    |    +--- org.apache.tomcat.embed:tomcat-embed-core:7.0.54
|    |    +--- org.apache.tomcat.embed:tomcat-embed-el:7.0.54
|    |    \--- org.apache.tomcat.embed:tomcat-embed-logging-juli:7.0.54
|    +--- com.fasterxml.jackson.core:jackson-databind:2.3.3
|    |    +--- com.fasterxml.jackson.core:jackson-annotations:2.3.0
|    |    \--- com.fasterxml.jackson.core:jackson-core:2.3.3
|    +--- org.hibernate:hibernate-validator:5.0.3.Final
|    |    +--- javax.validation:validation-api:1.1.0.Final
|    |    +--- org.jboss.logging:jboss-logging:3.1.1.GA
|    |    \--- com.fasterxml:classmate:1.0.0
|    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    +--- org.springframework:spring-web:4.0.6.RELEASE
|    |    +--- org.springframework:spring-aop:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|    |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    \--- org.springframework:spring-webmvc:4.0.6.RELEASE
|         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-expression:4.0.6.RELEASE (*)
|         \--- org.springframework:spring-web:4.0.6.RELEASE (*)
\--- org.slf4j:slf4j-api:1.7.1 -> 1.7.7

(*) - dependencies omitted (listed previously)

BUILD SUCCESSFUL

Total time: 3.622 secs
➜  gradle-dependency-insight git:(master) ✗
```

可以看到，虽然我们仅仅声明了两个依赖，但打印出了远超我们预期的内容。如何才能理解它？

按configuration分组
------------------

如果我们仔细观察上面的输出，它其实分成了几个组：

```
archives - Configuration for archive artifacts.
compile - Compile classpath for source set 'main'.
default - Configuration for default artifacts.
runtime - Runtime classpath for source set 'main'.
testCompile - Compile classpath for source set 'test'.
testRuntime - Runtime classpath for source set 'test'.
```

这里的组，实际上是当前`build.gradle`文件中，所使用的所有gradle plugin中定义的configuration。由于此处我们只使用了java plugin，所以只列出了上面这几个，如果用到其它的plugin，还会显示更多。

因为我们可以为不同的configuration设置依赖，所以保险起见，gradle把全部的组都列了出来。我们可以根据每个组的描述，只关注自己感兴趣的组。

树型结构
-------

每一个项目都可以引用多个依赖，而每一个依赖还可以有它自己的依赖，所以依赖之间是一个树形关系。

这里以`compile`组为例：

```
compile - Compile classpath for source set 'main'.
+--- org.springframework.boot:spring-boot-starter-web:1.1.5.RELEASE
|    +--- org.springframework.boot:spring-boot-starter:1.1.5.RELEASE
|    |    +--- org.springframework.boot:spring-boot:1.1.5.RELEASE
|    |    |    +--- org.springframework:spring-core:4.0.6.RELEASE
|    |    |    |    \--- commons-logging:commons-logging:1.1.3
|    |    |    \--- org.springframework:spring-context:4.0.6.RELEASE
|    |    |         +--- org.springframework:spring-aop:4.0.6.RELEASE
|    |    |         |    +--- aopalliance:aopalliance:1.0
|    |    |         |    +--- org.springframework:spring-beans:4.0.6.RELEASE
|    |    |         |    |    \--- org.springframework:spring-core:4.0.6.RELEAS (*)
|    |    |         |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    |         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    |         \--- org.springframework:spring-expression:4.0.6.RELEASE
|    |    |              \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-autoconfigure:1.1.5.RELEASE
|    |    |    \--- org.springframework.boot:spring-boot:1.1.5.RELEASE (*)
|    |    +--- org.springframework.boot:spring-boot-starter-logging:1.1.5.RELEASE
|    |    |    +--- org.slf4j:jcl-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:jul-to-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    +--- org.slf4j:log4j-over-slf4j:1.7.7
|    |    |    |    \--- org.slf4j:slf4j-api:1.7.7
|    |    |    \--- ch.qos.logback:logback-classic:1.1.2
|    |    |         +--- ch.qos.logback:logback-core:1.1.2
|    |    |         \--- org.slf4j:slf4j-api:1.7.6 -> 1.7.7
|    |    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    |    \--- org.yaml:snakeyaml:1.13
|    +--- org.springframework.boot:spring-boot-starter-tomcat:1.1.5.RELEASE
|    |    +--- org.apache.tomcat.embed:tomcat-embed-core:7.0.54
|    |    +--- org.apache.tomcat.embed:tomcat-embed-el:7.0.54
|    |    \--- org.apache.tomcat.embed:tomcat-embed-logging-juli:7.0.54
|    +--- com.fasterxml.jackson.core:jackson-databind:2.3.3
|    |    +--- com.fasterxml.jackson.core:jackson-annotations:2.3.0
|    |    \--- com.fasterxml.jackson.core:jackson-core:2.3.3
|    +--- org.hibernate:hibernate-validator:5.0.3.Final
|    |    +--- javax.validation:validation-api:1.1.0.Final
|    |    +--- org.jboss.logging:jboss-logging:3.1.1.GA
|    |    \--- com.fasterxml:classmate:1.0.0
|    +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    +--- org.springframework:spring-web:4.0.6.RELEASE
|    |    +--- org.springframework:spring-aop:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|    |    +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|    |    \--- org.springframework:spring-core:4.0.6.RELEASE (*)
|    \--- org.springframework:spring-webmvc:4.0.6.RELEASE
|         +--- org.springframework:spring-beans:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-context:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-core:4.0.6.RELEASE (*)
|         +--- org.springframework:spring-expression:4.0.6.RELEASE (*)
|         \--- org.springframework:spring-web:4.0.6.RELEASE (*)
\--- org.slf4j:slf4j-api:1.7.1 -> 1.7.7
```

可以看到，其结构就是一个树，跟我们用`tree somedir`时显示的内容差不多。

其中第一级的各节点，是我们显式在`build.gradle`中声明的，只有两个：

    org.springframework.boot:spring-boot-starter-web:1.1.5.RELEASE
    org.slf4j:slf4j-api:1.7.1

其它的都依次是它们的依赖，以及依赖的依赖。

`1.7.1 -> 1.7.7`
--------------

我们可以注意到，某些依赖的后面有以`->`连接的两个版本号，比如：

    org.slf4j:slf4j-api:1.7.1 -> 1.7.7

左边的版本号，是我们在`build.gradle`中声明的，或者是某个依赖在它自己的描述文件中显式声明的。而右边的版本号，是gradle根据所有的依赖信息进行冲突处理之后决定采用的版本号。

以这里为例，我们在`build.gradle`中，显式声明了`org.slf4j:slf4j-api:1.7.1`。但是在gradle处理`org.springframework.boot:spring-boot-starter-web:1.1.5.RELEASE`时，发现它经过层层依赖以后，使用了`org.slf4j:slf4j-api:1.7.7`。

对于gradle来说，当发现引用了同一个库的两个不同版本时，它会采用较新的版本，即`1.7`，同时在被抛弃的依赖旁边加上如`1.7.1 -> 1.7.7`的标记。

`(*)`
------

还有一些依赖在后面加上了`(*)`，如`org.springframework:spring-core:4.0.6.RELEASE (*)`。

它们的意思是说，该依赖在前面的解析中已经遇到并处理了，所以在这一步就直接忽略它了。

总结
====

通过这些标记，gradle清楚的告诉我们两个信息：

1. 当前真正使用的依赖（包括版本）是哪些
2. 依赖冲突是如何解决的

这对于我们了解项目依赖的真实情况很有帮助
