---
title: macos command
date: 2022-01-07 14:03:15
tags: command
categories: macos
---

- 解压带有中文名称的zip包

```shell
ditto -V -x -k --sequesterRsrc filename.zip destination
```

- 查看目录下文件夹大小
  
  ```shell
  du -d 1 -h    命令查看当前目录下所有文件夹的大小 -d 指深度，后面加一个数值
  ```
  
  
