---
title: python points
date: 2023-07-24 10:37:43
tags: points
categories: python
---



## 指令

- 安装tar.gz，解压之后，执行

  ```python
  python3 setup.py install
  ```

- 查看指定依赖版本

  ```python
  pip3 show package_name
  ```


- 把项目的依赖放入指定文件

  ```python
  pip freeze > requirements.txt
  ```

## 信创

- 安装python3-devel

  ```shell
  dnf install python3-devel
  ```


## 规范

- **请选择四个空格的缩进，不要使用 Tab，更不要 Tab 和空格混着用。**
- **全局的类和函数的上方需要空两个空行，而类的函数之间需要空一个空行**
- 代码的尾部，每个代码文件的最后一行为空行，并且只有这一个空行。
- 可以使用`#`进行单独注释，请记得要在`#`后、注释前加一个空格。

- 对于操作符，例如`+`，`-`，`*`，`/`，`&`，`|`，`=`，`==`，`!=`，请在两边都保留空格。不过与此对应，括号内的两端并不需要空格。
- 采用 `from module import func` 这样的语句，请确保 func 在本文件中不会出现命名冲突。不过，你其实可以通过 `from module import func as new_func` 来进行重命名，从而避免冲突。
- 如果是类的私有变量，请记得前面增加两个下划线。
- 对于常量，最好的做法是全部大写，并通过下划线连接
- 对于函数名，同样也请使用小写的方式，通过下划线连接起来，例如：`launch_nuclear_missile()`、`check_input_validation()`。

- 对于类名，则应该首字母大写，然后合并起来，例如：`class SpatialDropout2D()`、`class FeatureSet()`。
