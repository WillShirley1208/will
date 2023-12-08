---
title: git mergetool
date: 2023-12-08 11:21:03
tags: confict resolve
categories: git
---

# vimdiff

## 进入冲突解决窗口

```shell
git mergetool
```

窗口显示四部分内容

- LOCAL – this is file from the current branch 

- BASE – common ancestor, how file looked before both changes 
- REMOTE – file you are merging into your branch 
- MERGED – merge result, this is what gets saved in the repo

移动窗口方法

```
Ctrl w + h   # move to the split on the left 
Ctrl w + j   # move to the split below
Ctrl w + k   # move to the split on top
Ctrl w + l   # move to the split on the right
```

处理冲突

- 可移动到merge窗口去手动改

- 使用命令

  ```shell
  :diffg RE  # get from REMOTE
  :diffg BA  # get from BASE
  :diffg LO  # get from LOCAL
  ```

保存退出

```shell
:wqa
```

# Meld

todo

