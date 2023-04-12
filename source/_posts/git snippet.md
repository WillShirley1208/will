---
title: git skill
date: 2016-08-05 22:00:30
tags: skill
categories: git
---

#### Git修改.gitignore不生效

- 在git中，如果想忽略某个文件，不让这个文件提交到版本库中，可以修改根目录中的.gitignore文件
  
  但有时候把某些目录或者文件加入忽略规则，发现并未生效

- 未生效原因：.gitignore只能忽略那些原来没有被追踪(track)的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的

- 解决方案：先把本地缓存删除（改变成未track状态），然后提交。

```
git rm -r --cached <要忽略的具体文件或者目录> 或者 git rm -r --cached . 
git add .
git commit -m "update .gitignore"
```

#### 文件提交

- 提交单个文件
  
  ```shell
  git commit <file> -m "your comment" 
  git pull
  git push
  ```

- 提交多个文件，但是需要排除指定的文件
  
  ```shell
  git add -u
  git reset -- main/dontcheckmein.txt
  然后进行commit、pull等操作
  ```

#### Git fork后的分支，更新最新的源代码

```shell
sourcer为源项目代码
forker为fork之后的项目代码

1、找一个空的目录下签出 fork 后的代码
git clone https://github.com/sourcer/demo.git
查看 remote 信息
git remote -v

2、然后添加源项目地址（距离定义为 source）
git remote add source https://github.com/forker/demo.git
查看 remote 信息，可以看到新增两条信息
git remote -v

3、fetch 源项目
git fetch source

4、合并代码
git merge source/master

5、把合并最新的代码推送到你的fork项目上
git push origin master
```

#### 比较文件，在不同版本的区别

```shell
# uncommited file to HEAD
git diff <path>

# uncommited file to before last commit
git diff HEAD^ -- <path>

#last commit to before last commit
git diff HEAD^ HEAD -- <path>

#difference between HEAD and n-th grandparent
git diff HEAD~n HEAD -- <path>

#Another cool feature is whatchanged command
git whatchanged -- <path>
```

#### 回滚远程分支

1、本地代码回滚到上一版本

> git reset --hard HEAD~1

（或者回滚到指定版本commitId）

> git reset --hard commitId

2、加入-f参数，强制提交，远程端将强制跟新到reset版本

>  git push -f

#### 拉取远程分支

First, fetch the remote branches:

**git fetch origin**

Next, checkout the branch you want. In this case, the branch we want is called “branchxyz”.

**git checkout -b branchxyz origin/branchxyz**

#### 新建分支

- To create a new branch from a branch you do NOT have checked out:
  
  `git branch new_branch from_branch`

- To create a new branch from the branch you DO have checked out:
  
  `git branch new_branch`

- To create *and check out* a new branch from the branch you DO have checked out:
  
  `git checkout -b new_branch`

- To create *and check out* a new branch from a branch you do NOT have checked out:
  
  `git checkout -b new_branch from_branch`

- To rename a branch
  
  `git branch -m old_name new_name`

#### 推送新分支到远程

在新建分支完成的前提下

1. 将develop分支推送到远程
   
   `git push origin new_branch:new_branch`

2. 建立本地至上游（远程）分支的链接
   
   `git branch --set-upstream-to=orgin/new_branch new_branch`

#### 删除分支

```shell
// delete branch locally
git branch -d localBranchName

// delete branch remotely
git push origin --delete remoteBranchName

//If someone else has already deleted the branch, you just do below
git fetch -p
```

#### 重命名分支

```shell
git branch -m new-branch-name
```

#### 合并策略

warning: 不建议在没有为偏离分支指定合并策略时执行pull操作。  
您可以在执行下一次pull操作之前执行下面一条命令来抑制本消息：

```shell
git config pull.rebase false # 合并（缺省策略）  
git config pull.rebase true # 变基  
git config pull.ff only # 仅快进
```

您可以将 "git config" 替换为 "git config --global" 以便为所有仓库设置  
缺省的配置项。您也可以在每次执行 pull 命令时添加 --rebase、--no-rebase，  
或者 --ff-only 参数覆盖缺省设置。

#### 推送本地离线项目到远程github

```shell
mkdir my_project
cd my_project
touch .gitignore
git init
git add .
git commit -m "Initial commit"
git remote add origin youruser@yourserver.com:/path/to/my_project.git
git push origin master
```

#### 查看本地分支与远程分支的关联

`git branch -vv`

#### 查看每一行是哪次提交最后修改的

```shell
git blame filename 
```

#### 列出文件的所有修改记录

```shell
git log -p filename
```

# 更改https协议进行推送

> 如果之前是使用ssh协议进行推送，现改为http协议

1. 查看当前Git项目的远程地址

   ```shell
   git remote -v
   ```

   该命令会列出所有已配置的远程仓库地址，并显示它们的读取和写入访问URL。示例输出如下所示：

   ```shell
   origin  https://github.com/username/repository.git (fetch)
   origin  https://github.com/username/repository.git (push)
   ```

   其中，`origin`是远程仓库的名称，`https://github.com/username/repository.git`是远程仓库的地址，`(fetch)`和`(push)`分别表示读取和写入访问URL。

2. 将Git仓库的URL替换为HTTP协议的URL

   ```shell
   git remote set-url origin http://gitlab.xxx.com/username/repository.git
   ```

3. 然后使用以下命令进行Git push操作

后记：需要注意的是，使用HTTP协议进行Git push操作的速度可能会比使用SSH协议慢一些，因为HTTP协议需要建立TCP连接、发送HTTP请求、接收HTTP响应等过程。同时，HTTP协议的安全性也比SSH协议稍低，因此在安全性要求较高的情况下，建议使用SSH协议进行Git push操作。

# stash命令

在Git中，`stash`命令用于暂存当前未提交的代码修改，以便在后续时刻再次应用它们。它可以暂存已修改但还未添加到暂存区的文件，也可以暂存已经添加到暂存区但还未提交的文件。

以下是`stash`命令的使用方式：

1. 暂存当前修改：(这个命令将当前的修改暂存起来，让你的工作目录变成干净的状态。)

   ```shell
   git stash
   ```

2. 恢复暂存的修改：

   ```shell
   git stash apply
   ```

   这个命令将之前暂存的修改应用到当前分支上。如果你有多个stash，可以使用`git stash apply stash@{n}`来指定恢复某个特定的stash。

3. 删除stash：

   ```shel
   git stash drop
   ```

   这个命令会删除最近的一个stash。

4. 列出所有stash：

   ```SHELL
   git stash list
   ```

5. 应用并删除stash：

   ```shell
   git stash pop
   ```

   这个命令相当于`git stash apply`和`git stash drop`的组合，它会将最近的一个stash应用到当前分支，并将其删除。

6. 暂存特定的文件：

   ```bash
   git stash push path/to/file
   ```

   这个命令可以将指定的文件暂存起来，而不是暂存整个工作目录。

7. 给stash添加描述：

   ```BASH
   git stash save "stash message"
   ```

   