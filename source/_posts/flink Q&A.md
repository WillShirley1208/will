---
title: flink Q&A
date: 2023-09-01 11:29:50
tags: Q&A
categories: flink
---

## 依赖冲突

https://flink.apache.org/getting-help/#i-see-a-classcastexception-x-cannot-be-cast-to-x

https://nightlies.apache.org/flink/flink-docs-release-1.17/docs/ops/debugging/debugging_classloading



排查办法

- 使用arthas定位依赖类所在jar包

解决办法

- 对冲突jar包使用maven shaded 的exclude或relocation 进行操作

- 使用7zip等解压软件对jar文件解压删除（人工shaded）