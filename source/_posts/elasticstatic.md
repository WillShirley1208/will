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



### solution

#### max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]

If you want to increase the limit shown by `ulimit -n`, you should:

- Modify `/etc/systemd/user.conf` and `/etc/systemd/system.conf` with the following line (this takes care of graphical login):

  ```
   DefaultLimitNOFILE=65535
  ```

- Modify `/etc/security/limits.conf` with the following lines (this takes care of non-GUI login):

  ```shell
   * hard nofile 65535
   * soft nofile 65535
  ```

- Reboot your computer for changes to take effect.

- check

  ```shell
  ulimit -Hn
  ulimit -Sn
  ```

  

#### max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

- `vim /etc/sysctl.conf `

  新增**vm.max_map_count=655360**

- `sysctl -p`



#### the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured

in short, if you are running Elasticsearch locally(single node) or just with a single node on the cloud then just use below config in your `elasticsearch.yml` to avoid the production check, and to make it work, more info about this config in [this SO](https://stackoverflow.com/a/60426167/4039431) answer:

```yaml
discovery.type: single-node
```

