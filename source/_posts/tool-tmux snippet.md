---
title: tmux snippet
date: 2023-11-15 11:48:27
tags: snippet
categories: tmux
---


- session
| 功能         | macos                        | linux                   |
| ------------ | ---------------------------- | ----------------------- |
| 新建session  | tmux new -s <session-name>   | 一样                    |
|              |                              |                         |
- 窗口

| 功能 | macos | linux |
| ---- | ----- | ----- |
|      |       |       |

- 窗格

| 功能         | macos                  | linux                   |
| ------------ | ---------------------- | ----------------------- |
| 调整窗格大小 | Ctrl + b, Esc + 方向键 | Ctrl + b, Ctrl + 方向键 |
| 窗格内容翻页 | Ctrl + b, Fn + 上下键  | Ctrl + b, Ctrl + 上下键 |
|              |                        |                         |

- 其它
| 功能         | macos                        | linux |
| ------------ | ---------------------------- | ----- |
| 内嵌tmux操作 | **按着 ctrl，点击两次 b 键** | 一样  |
|              |                              |       |




# 命令模式

`ctrl + b` 然后按下`:`

## 会话

- 创建会话

  `new-session -s <session name>` 

- 切换会话

  `switch-client -t <session name or id>`

## 窗口

- 交换窗口位置

  ```shell
  # 交换编号3与编号1窗口的位置
  swap-window -s 3 -t 1
  
  # 把当前窗口放到指定编号1的位置
  swap-window -t 0
  ```

  

- 新建窗口

  `new-window -n <window name>`

- 切换窗口

  `select-window -t <window id>`

  `select-window -n <window name>`

## 窗格

- 分割窗格

  `split-window -h` 水平

  `split-window -v` 垂直

