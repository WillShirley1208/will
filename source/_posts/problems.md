---
title: tmp problems
date: 2022-09-27 17:33:47
tags: problems
categories: scratch
---

# DB2

## DB2 SQL Error: SQLCODE=-805, SQLSTATE=51002, SQLERRMC=NULLID.SYSLH2DA 0X5359534C564C3031

- 分析

  the problem was a list of operations made with the same PreparedStatement, which was never closed.

  许多操作使用同一个PreparedStatement，但是重来没有关闭

- 参考

  https://cloud.tencent.com/developer/article/1837198

# 日志

## lambda是否可打日志