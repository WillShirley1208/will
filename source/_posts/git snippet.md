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

```
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

```
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