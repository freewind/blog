---
layout: post
title: Play2中如何实现play1中的Job?
tags: Play 2
date: 2013-01-01 17:04:55
---

在play1中，我们可以这样定义一些Job，让它们在后台执行：

    @OnApplicationStart
    @Every("1h")
    public class MyJob extends Job {
        public void doJob() {
            println("Hello world");
        }
    }

    以上代码将每小时向控制台输出一次`Hello world`。

    但在play2中找不到`OnApplicationStart`和`Every`了，应该怎么实现该功能？

    **回答：**

    `OnApplicationStart`的功能移到`GlobalSettings`中了。我们需要在根包下建立一个`Global`类继承于它，并覆盖其`onStart`方法。

    而`Every`这样的`Job`功能，现在使用Akka来处理了。我们需要向Akka提交一些异步任务。

    代码如下：

    import akka.actor.ActorSystem;
    import akka.actor.Scheduler;
    import akka.util.Duration;
    import akka.util.FiniteDuration;
    import java.util.concurrent.TimeUnit;
    import play.Application;
    import play.GlobalSettings;
    import play.libs.Akka;

    class Global extends GlobalSettings {
        public void onStart(Application app) {
            Akka.system().scheduler().schedule(
                Duration.create(0, TimeUnit.MILLISECONDS),
                Duration.create(1, TimeUnit.HOURS),
                new Runnable() {
                    public void run() {
                        System.out.println("Hello world");
                    }
                }
            );
        }
    }
