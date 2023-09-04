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