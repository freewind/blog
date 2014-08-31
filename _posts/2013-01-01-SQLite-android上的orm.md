---
layout: post
alias: android-orm-for-sqlite
tags: Android
date: 2013-01-01 11:22:50
title: SQLite: android上的orm
---

Android内置了sqlite作为数据库，但没有提供jdbc。它提供了一些类来执行sql的增删改，但对于查，却是提供了一个query方法，而不能直接通过sql查询（需验证）。

这个query方法有七八个参数，用起来十分不方便。而有时候项目中需要的表还比较多（比如我这个明信片，在android上就需要十来个表），如果只用这个query，实在太低效了。

这个时候还是得用orm。

但Android上的vm跟jvm完全不一样，导致我们通常用的那些orm都不能用了，比如我最喜欢的Ebean。好在有些人写了一些轻量级的可用于anroid的orm，这个SQLite即是一个。

另外还看到一个：[http://code.google.com/p/droidpersistence](http://code.google.com/p/droidpersistence)，这个是gpl3的，所以不推荐。

Ormlite是自定的开源协议，没有要求商业开源，所以可以放心使用：[http://ormlite.com/javadoc/ormlite-core/doc-files/ormlite_9.html#SEC70](http://ormlite.com/javadoc/ormlite-core/doc-files/ormlite_9.html#SEC70)

使用Ormlite的原因之一，是作者在stackoverflow上的贡献值已有 25k+，还是比较靠谱的。
