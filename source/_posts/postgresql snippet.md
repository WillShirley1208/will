---
title: postgresql snippet
date: 2021-12-01 10:30:22
tags: snippet
categories: fragment
---



### 终端登录pg

- 如果之前没有登录过，需要设置当前用户进行登录操作

  There is no default username and password without you creating one. The simplest possible setup is to follow these steps to set up your own user as a superuser.

  At a terminal prompt, create a postgres user with your own username

  ```
  sudo -u postgres createuser --superuser $USER	# $USER无须替换
  ```

  Start the postgresql command prompt as your username but running as root since you didn't set a password yet;

  ```
  sudo -u postgres psql
  ```

  At the postgresql prompt, set your password;

  ```
  \password $USER    # 其中$USER需要替换成当前用户名
  ```

  After that, you should be able to log on just fine.



- 如果之前设置了上面的步骤，可直接运行

  ```、
  psql postgres
  ```

  

### 导入文件

```shell
psql postgres	# login command
\c some_database	# choose database
\i \path\TO\file_name.sql	# execute sql
```
