---
title: tmux snippet
date: 2023-11-15 11:48:27
tags: snippet
categories: tmux
---



| 功能         | macos                        | linux                   |
| ------------ | ---------------------------- | ----------------------- |
| 新建session  | tmux new -s <session-name>   | 一样                    |
| 调整窗格大小 | Ctrl + b, Esc + 方向键       | Ctrl + b, Ctrl + 方向键 |
| 窗格内容翻页 | Ctrl + b, Fn + 上下键        | Ctrl + b, Ctrl + 上下键 |
| 内嵌tmux操作 | **按着 ctrl，点击两次 b 键** | 一样                    |
|              |                              |                         |
|              |                              |                         |
|              |                              |                         |
|              |                              |                         |
|              |                              |                         |
|              |                              |                         |



# 命令模式

`ctrl + b` 然后按下`:`

- 创建会话

  `new-session -s <session name>` 

- 切换会话

  `switch-client -t <session name or id>`

- 新建窗口

  `new-window -n <window name>`

- 切换窗口

  `select-window -t <window id>`

  `select-window -n <window name>`

- 分割窗格

  `split-window -h` 水平

  `split-window -v` 垂直

