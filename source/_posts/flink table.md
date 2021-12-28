---
title: flink table
date: 2021-12-24 09:00:00
tags: learn
categories: flink
---



### format

- timestamp

  可以将时间戳类型数据最高精确微秒(百万分之一秒)，数据类型定义为timestamp(N),N取值范围为0-6，默认为0，如需要精确到毫秒则设置为Timestamp(3)，如需要精确到微秒则设置为timestamp(6)，数据精度提高的代价是其内部存储空间的变大，但仍未改变时间戳类型的最小和最大取值范围。



### connector kafka

