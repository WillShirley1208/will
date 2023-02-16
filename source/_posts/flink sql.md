---
title: flink sql
date: 2022-01-04 17:55:24
tags: point
categories: flink
---

# table vs view

> Temporary tables are always stored in memory and only exist for the duration of the Flink session they are created within. These tables are not visible to other sessions. They are not bound to any catalog or database but can be created in the namespace of one. Temporary tables are not dropped if their corresponding database is removed.

> Tables can be either virtual (VIEWS) or regular (TABLES). VIEWS can be created from an existing Table object, usually the result of a Table API or SQL query. TABLES describe external data, such as a file, database table, or message queue.

# grammer

- NOT ENFORCED

> If you know that the data conforms to these constraints, you can use the NOT ENFORCED capability to help achieve two goals:
> 
> - Improve performance, primarily in insert, update, and delete operations on the table
> - Reduce space requirements that are associated with enforcing a primary key or unique constraint

