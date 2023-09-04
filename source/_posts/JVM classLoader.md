---
title: JVM class loader
date: 2023-08-31 16:06:42
tags: command
categories: jvm
---

## class冲突

可以打印出类的加载顺序，可以用来排查 class 的冲突问题：

```java
-XX:+TraceClassLoading
```



