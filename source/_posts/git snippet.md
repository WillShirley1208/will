---
title: git skill
date: 2016-08-05 22:00:30
tags: skill
categories: git
---

- Git修改.gitignore不生效 

  - 在git中，如果想忽略某个文件，不让这个文件提交到版本库中，可以修改根目录中的.gitignore文件

    但有时候把某些目录或者文件加入忽略规则，发现并未生效

  - 未生效原因：.gitignore只能忽略那些原来没有被追踪(track)的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的

  - 解决方案：先把本地缓存删除（改变成未track状态），然后提交。

```
git rm -r --cached <要忽略的具体文件或者目录> 或者 git rm -r --cached . 
git add .
git commit -m "update .gitignore"
```

