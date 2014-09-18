---
layout: post
id: 2769
alias: sbt-resolvers
tags: sbt
date: 2014-09-13 13:53:32
title: sbt的resolvers
---

```
resolvers += "Local Maven Repository" at "file://"+Path.userHome.
   absolutePath+"/.m2/repository"
```