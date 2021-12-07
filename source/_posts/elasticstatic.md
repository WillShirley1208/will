---
title: Elastic Stack learn
date: 2021-12-03 09:26:30
tags: learn
categories: Elasticsearch
---

**ELK**

> Elasticsearch 是一个搜索和分析引擎。Logstash 是服务器端数据处理管道，能够同时从多个来源采集数据，转换数据，然后将数据发送到诸如 Elasticsearch 等“存储库”中。Kibana 则可以让用户在 Elasticsearch 中使用图形和图表对数据进行可视化。

Elasticsearch is the living heart of what is today’s the most popular log analytics platform — the ELK Stack (Elasticsearch, Logstash and Kibana). Elasticsearch’s role is so central that it has become synonymous with the name of the stack itself.

Elasticsearch behaves like a REST API, so you can use either the `POST` or the `PUT` method to add data to it. You use `PUT` when you know the or want to specify the `id` of the data item, or `POST` if you want Elasticsearch to generate an `id` for the data item: