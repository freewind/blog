---
layout: post
title: 如何快速删除postgresql数据库所有的表
tags: More ...
date: 2012-02-05 16:42:33
---

在postgesql中建立一个函数：

> CREATE OR REPLACE FUNCTION truncate_tables(username IN VARCHAR) RETURNS void AS $$     
> DECLARE      
>     statements CURSOR FOR      
>         SELECT tablename FROM pg_tables      
>         WHERE tableowner = username AND schemaname = 'public';      
> BEGIN      
>     FOR stmt IN statements LOOP      
>         EXECUTE 'TRUNCATE TABLE ' || quote_ident(stmt.tablename) || ' CASCADE;';      
>     END LOOP;      
> END;      
> $$ LANGUAGE plpgsql;
> 
>  

然后调用：

> <font style="background-color: #ffffff">select truncate_tables('username')</font>

即可。

可以在单元测试中使用这个方法用于初始化，很方便
