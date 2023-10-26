---
title: flink upsert stream
date: 2023-08-10 10:47:36
tags: learn
categories: flink
---



# upsert kafka

The Kafka connector in Flink SQL can work in two streaming modes. 

**Upsert mode** allows us to get the latest value for a specific entity automatically without any manual deduplication. One of the typical scenarios where you can leverage this mode is a SQL join of two tables, where one of the tables is keeping history of changes per some entity id. Once you join on such an entity id which is non-unique by design, you get unwanted rows, but you usually want to see the latest value of that entity. With upsert mode, Flink automatically normalizes before the tables are joined. Eventually it allows you to easily answer typical business questions on getting a real-time view of the shared resources like cars, planes, workers, etc.

**Append mode** is still an option to go with, if a business query does not need to filter out all historical events, but rather show the history of changes at the end. In this scenario, query may run faster with append mode, as Flink does not need to do any changelog normalization.

## tips

- upsert-kafka 可以配合 checkpoint使用，这样不需要再从头消费之前的数据

- 如果kafka中key没有值，是不符合upsert-kafka的格式的，flink sql引用此类topic为upsert-kafka，则不会触发计算

## deduplication

```sql
SELECT [column_list]
FROM (
   SELECT [column_list],
     ROW_NUMBER() OVER ([PARTITION BY col1[, col2...]]
       ORDER BY time_attr [asc|desc]) AS rownum
   FROM table_name)
WHERE rownum = 1
```

- 注意 在内嵌的 select语句中，建议使用 column_list 查询指定的字段，不要使用 * 代指所有指标。否则查询upsert-kafka会，一直从最新的记录读取。