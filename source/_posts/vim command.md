---
title: vim command
date: 2015-08-16 12:01:30
tags: vim
categories: linux
---

- format json
  
  ```shell
  :%!jq .
  ```

- formate xml
  
  ```
  :%!xmllint --format %
  ```

- 显示不可见字符
  
  ```shell
  :set list
  ```

- 统计字符出现次数

  ```
  :%s/字符串//ng
  ```

  