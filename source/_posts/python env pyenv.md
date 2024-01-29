---
title: python pyenv
date: 2023-11-21 10:57:00
tags: pyenv
categories: python
---

```shell
# 查看当前版本
pyenv version

# 查看所有版本
pyenv versions

# 查看所有可安装的版本
pyenv install --list

# 安装指定版本
pyenv install 3.6.5
# 安装新版本后rehash一下
pyenv rehash

# 删除指定版本
pyenv uninstall 3.5.2

# 指定版本
pyenv shell <version> -- select just for current shell session
pyenv local <version> -- automatically select whenever you are in the current directory (or its subdirectories)
pyenv global <version> -- select globally for your user account

# 指定多个全局版本, 3版本优先
pyenv global 3.6.5 2.7.14

# 实际上当你切换版本后, 相应的pip和包仓库都是会自动切换过去的
```

- 当前项目使用的pyenv版本会在 `.python-version`文件中体现

---

reference

- [specifying-your-python-version](https://realpython.com/intro-to-pyenv/#specifying-your-python-version)