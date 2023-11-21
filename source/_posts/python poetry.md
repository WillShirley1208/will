---
title: python poetry
date: 2023-11-03 02:00:28
tags: lib
categories: python
---

## TODO

- [ ] build backend : use hatch 
  - https://stackoverflow.com/questions/75408641/whats-the-difference-between-the-tool-poetry-and-project-tables-in-pyprojec



## POINT 

> 每个使用 Poetry 的项目都会有自己的虚拟环境，这样可以避免不同项目之间的依赖冲突。因此，每个项目的依赖项都会独立存放在各自的虚拟环境中。

- 查看安装依赖目录

  ```shell
  poetry env info --path
  ```

- 列出所有虚拟环境

  ```shell
  poetry env list
  ```

- 删除指定虚拟环境

  ```shell
  poetry env remove <虚拟环境名称>
  ```

- 进入 Poetry 创建的虚拟环境

  ```
  poetry shell
  ```

  

- 通过`requirement`添加依赖

  ```shell
  poetry add $( cat requirements.txt )
  ```

  







---

**Reference**

- [THE BASICS OF PYTHON PACKAGING IN EARLY](https://drivendata.co/blog/python-packaging-2023)

  