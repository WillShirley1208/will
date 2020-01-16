---
title: rabbitmq learn
date: 2019-05-14 11:45:30
tags: learn
categories: rabbitmq
---

- 查询已有queue

  `sudo rabbitmqctl list_queues`

- 查询已有exchanges

  `sudo rabbitmqctl list_exchanges`

## Publish/Subscribe

- Exchanges

  There are a few exchange types available: **direct**, **topic**, **headers** and **fanout**.

- Bindings

  A binding is a relationship between an exchange and a queue