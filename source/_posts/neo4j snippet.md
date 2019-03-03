---
title: neo4j snippet
date: 2017-11-21 17:17
tags: snippet
categories: neo4j
---

- cypher语法对大小写敏感，注意属性的大小写
- 导入csv出现乱码时，需要通过记事本更改编码格式，如果文本过大，需要一段一段的复制粘贴到新文本中，且以UTF-8格式编码
- MERGE命令是CREATE命令和MATCH命令的组合。`MERGE = CREATE + MATCH`


## 问题 ##
1.
    **现象**： 在外部处理csv数据，导入服务器，然后load csv到neo4j中，可能由于文件所属问题，不能成功导入csv，提示`Couldn't load the external resource`的错误。
    **解决**：复制可以导入的csv文件为副本，把待导入的数据粘贴到副本中，load csv副本即可

2.
    **现象**： 在外部处理csv数据，进行merge操作的时候，提示`cannot merge using null property value for uid`的错误。
    **解决**：经发现，是由于指令中的属性拼写错误，导致`Neo.ClientError.Statement.SemanticError`的错误，例如把`sfzh`拼写成`sfhm`。

3.
    **现象**： 大量数据的导入问题。
    **解决**：需使用[https://neo4j.com/docs/operations-manual/current/tools/import/](https://neo4j.com/docs/operations-manual/current/tools/import/ "大量数据的导入")
    **注意**：`Import tool is only for newly created databases.`
		`You can use LOAD CSV cypher command to load into an existing database.`也就是说neo4j-admin import只会在新建数据库的时候会用到