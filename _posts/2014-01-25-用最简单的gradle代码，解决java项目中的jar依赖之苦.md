---
layout: post
title: 用最简单的gradle代码，解决java项目中的jar依赖之苦
tags:
  - Java
date: 2014-01-25 16:41:51
---

有时候我们会随手建立一些小的java项目，写点代码，尝试点什么功能。如果使用到什么第三方库，把它们导入起来总觉得是一件麻烦事：

*   手动下载jar包，在IDE中层层设置，还要处理它们的依赖
*   使用IDE提供的从maven库下载
*   使用maven, gradle等构建工具

今天我尝试从零开始，使用最简单的gradle代码来解决这个麻烦，结果让人开心，比较方便。

### 下载gradle

在mac下使用brew:

     brew install gradle
    

    然后可输入：

    gradle
    

    看看是否安装成功。

    在linux下有apt-get或yum可用，windows可能也有类似工具，不过多数时候还是手动下载了。

    这是个一次性的工作，麻烦一次即可。

    ### 在项目下建立build.gradle文件

    然后输入内容：

    apply plugin: 'java'

    repositories {
        mavenCentral()
    }

    dependencies {
        compile(
                'org.apache.commons:commons-lang3:3.2.1',
                'org.apache.commons:commons-io:1.3.2',
                'com.google.guava:guava:16.0'
        )
        testCompile(
            'junit:junit:4.11'
        )
    }
    

    依赖就定义好了。

    小技巧：需要jar包时，可到 http://search.maven.org 上搜一下，将上面的格式写过来就行了

    ### 生成idea或eclipse项目文件

    对于intellij-idea，加入：

    apply plugin: 'idea'
    

    然后到项目根目录，输入：

    gradle idea
    

    它就会自动下载依赖并生成idea需要的文件，全部搞定。

    如果是eclipse，则加入:

    apply plugin: 'eclipse'
    

    再进行：

    gradle eclipse

即可。

上面的内容相当简单，背下来即可。如果不要求更多，这些内容就够用了。

Jetty

如果我们的项目是一个web项目，可以添加jetty插件，快速启动一个jetty server。

只需要在build.gradle中添加：

apply plugin: &#8220;jetty&#8221;

即可。然后执行：

gradle jettyRun

就会启动server，默认以8080端口提供http服务。

wrapper

有时候我们希望在项目中提供一个gradle脚本，这样用户在clone下代码库后，就算机器上没有装gradle，也可以快速执行。

我们需要在build.gradle中添加一个task:

task wrapper(type: Wrapper) {

gradleVersion = '1.11&#8242;

}

里面可以指定使用的gradle版本，我这里写的是最新版1.11

然后在命令行执行：

gradle wrapper

就会自动在项目下生成几个文件：

- gradlew

- gradlew.bat

以及相应的jar。

把这些文件都提交到服务器上即可。

用户使用时，需要使用当前项目中的gradlew命令去执行gradle操作，比如：

./gradlew idea

./gradlew jettyRun