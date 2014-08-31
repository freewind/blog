---
layout: post
id: 2746
alias: cannot-connect-to-postgresql-tmp-s-pgsql-5432
tags: Linux
date: 2014-08-31 13:59:16
title: 在mac上连接postgresql出现”/tmp/.s.PGSQL.5432″错误，无法连接
---

在mac上使用`psql`命令连接postgresql，出现以下错误：


```
$> psql
psql: could not connect to server: Too many levels of symbolic links
Is the server running locally and accepting
connections on Unix domain socket “/tmp/.s.PGSQL.5432″?
```

反复搜索，卸载了又装，都解决不了问题，但是通过图形工具pgAdmin连接又一切正常。

最后找到了这个解决办法：

    export PGHOST=localhost

就一切正常了。

可以把它加入到`~/.bashrc`中
