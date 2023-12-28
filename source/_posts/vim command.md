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


- 大小写转换

  ```
  选择文本之后，执行 g~~ 进行大小写的转换
  ```

- 全文选择

- 多行操作

  ```
  按下 Ctrl + v 进入可视块模式。
  使用上下箭头键或 j 和 k 键选择要编辑的行。
  按下 Shift + i 进入插入模式。
  输入要插入的文本。
  按下 Esc 键退出插入模式，所有选定的行都将被修改。
  ```

  

  