---
title: python conda
date: 2023-12-18 14:10:58
tags: conda
categories: python
---

> conda相当于虚拟机，在里面安装python和依赖

- 查看环境

  ```
  conda env list
  ```

- 新建环境

  ```
  conda create --name your_env_name
  ```

  指定python版本

  ```
  conda create --name xxx python=3.10
  ```

  

- 激活环境

  ```
  conda activate xxx
  ```

  

- 删除环境

  ```
  conda remove --name xxx --all
  ```

  