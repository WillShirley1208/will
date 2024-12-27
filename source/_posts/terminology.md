---
title: terminology
date: 2024-12-19 15:52:40
tags: terminology
categories: basic
---

# 幂等/非幂等

在计算机科学和网络通信中，幂等（Idempotent）和非幂等（Non-idempotent）描述了操作执行多次和执行一次的效果是否相同。

1. **幂等操作**：
   - **定义**：如果一个操作执行多次和执行一次的效果相同，那么这个操作被称为幂等操作。
   - **特点**：幂等操作在重复执行时不会改变系统状态，即多次执行和执行一次的结果是相同的。
   - **例子**：
     - **GET请求**：读取资源的操作是幂等的，因为无论读取多少次，资源的内容不会改变。
     - **PUT请求**：更新资源的操作通常是幂等的，因为多次更新同一个资源到相同的状态，最终资源的状态不会改变。
     - **DELETE请求**：删除资源的操作也是幂等的，因为一旦资源被删除，再次删除不会对系统产生进一步的影响。

2. **非幂等操作**：
   - **定义**：如果一个操作执行多次和执行一次的效果不同，那么这个操作被称为非幂等操作。
   - **特点**：非幂等操作在重复执行时会改变系统状态，即多次执行和执行一次的结果不同。
   - **例子**：
     - **POST请求**：创建资源的操作是非幂等的，因为多次执行创建操作会创建多个资源实例。
     - **某些类型的UPDATE请求**：如果更新操作涉及到计数器或其他会随时间变化的值，那么这些操作可能是非幂等的，因为多次更新可能会累积效果。

在分布式系统和网络通信中，幂等性是一个重要的属性，因为它可以帮助确保系统的一致性和可靠性。例如，在网络请求中，如果一个请求由于网络问题被重复发送，幂等操作可以确保系统不会因为重复的请求而产生不一致的状态。