---
title: shell bash
date: 2024-07-30 18:39:34
tags: bash
categories: shell
---

- the shell assigns its **exit code** to the `$?` environment variable. The `$?` variable is how we usually test whether a script has succeeded or not in its execution.

# point

- `(cd third-party && ...)` 这样的语法，这是一个命令替换，它实质上是先临时改变到 `third-party` 目录执行里面的命令，然后脚本会继续在原先的目录中执行。