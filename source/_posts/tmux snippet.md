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



# Tmux Plugin Manager (TPM)

- 配置文件地址`~/.tmux.conf`

  修改配置生效 `tmux source-file ~/.tmux.conf`

- ##### 使用快捷键插件管理

  ```
  # prefix表示是Tmux的快捷键前缀
  prefix shift-i      # 安装件列表中的插件
  prefix shift-u      # 更新件列表中的插件
  prefix alt-u        # 删除/卸载不在插件列表中的插件
  ```

- ##### 使用命令行插件管理

  ```
  ~/.tmux/plugins/tpm/bin/install_plugins                  # 安装件列表中的插件
  ~/.tmux/plugins/tpm/bin/update_plugins all|plugname      # 更新所有插件或指定插件
  ~/.tmux/plugins/tpm/bin/clean_plugins                    # 删除/卸载不在插件列表中的插件
  ```

  

- ##### 保存/恢复会话 tmux-resurrect

  > 默认会话数据路径 /.local/share/tmux/resurrect

  ```
  保存Tmux会话：前缀键 + Ctrl-s
  还原Tmux会话： 前缀键 + Ctrl-r
  
  Tmux Resurrect 会将 Tmux 会话的详细信息以文本文件形式保存到 ~/.tmux/resurrect 目录；
  ```

  
