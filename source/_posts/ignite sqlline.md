---
title: ignite sqlline
date: 2023-09-25 19:00:45
tags: command
categories: ignite
---

> TIPS:
>
> 1. Keep in mind that, in Ignite, the concepts of a SQL table and a key-value cache are two equivalent representations of the same (internal) data structure. You can access your data using either the key-value API or SQL statements, or both.
>
> 2. A cache is a collection of key-value pairs that can be accessed through the key-value API. A SQL table in Ignite corresponds to the notion of tables in traditional RDBMSs with some additional constraints; for example, each SQL table must have a primary key.
>
>    A table with a primary key can be presented as a key-value cache, in which the primary key column serves as the key, and the rest of the table columns represent the fields of the object (the value).



## 登录

```shell
$IGNITE_HOME/bin/sqlline.sh --verbose=true -u jdbc:ignite:thin://ip地址/PUBLIC
```

