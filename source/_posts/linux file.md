---
title: linux file command
date: 2015-10-16 12:01:30
tags: command
categories: linux

---

## delete

**1.** To delete all files in a directory except filename, type the command below:

```shell
rm -v !("filename")
```

**2.** To delete all files with the exception of **filename1** and **filename2**:

```shell
rm -v !("filename1"|"filename2") 
```

**3.** The example below shows how to remove all files other than all `.zip` files interactively:

```shell
rm -i !(*.zip)
```

**4.** Next, you can delete all files in a directory apart from all `.zip` and `.odt` files as follows, while displaying what is being done:

```shell
rm -v !(*.zip|*.odt)
```

## AWK

- `awk -v FS="输入分隔符" -v OFS='输出分隔符' '{if($1==$5) print $1,$5,$10}' filename`
  
  查找filename文件（文件中列的分隔符为“输入分隔符”）中，每一行第一列和第五列相等的行，并输出第一列、第五列、第十列，切输出字段分隔符为“输出分隔符”。如果不配置FS和OFS，那么输入输出分隔符均默认为空

- [exclude a column with awk](https://www.commandlinefu.com/commands/view/6872/exclude-a-column-with-awk), 比如打印除第5列的其它所有列
  
  awk '{ $5=""; print }' file

## 统计文件行数

语法：wc [选项] 文件…

说明：该命令统计给定文件中的字节数、字数、行数。如果没有给出文件名，则从标准输入读取。wc同时也给出所有指定文件的总统计数。字是由空格字符区分开的最大字符串。

该命令各选项含义如下：

　　- c 统计字节数。

　　- l 统计行数。

　　- w 统计字数。

这些选项可以组合使用。

## 权限

使文件可以直接执行的命令：chmod +x filename

使所有用户对目录都有读写权限：sudo chmod ugo+rw /opt

```
r=4，w=2，x=1
若要rwx属性则4+2+1=7；
若要rw-属性则4+2=6；
若要r-x属性则4+1=7
```

## 文件分割

```shell
split [-a] [-b] [-C] [-l] [要分割的文件名] [分割后的文件名前缀]
–version 显示版本信息
– 或者-l,指定每多少行切割一次，用于文本文件分割
-b 指定切割文件大小,单位 m 或 k
-C 与-b类似，但尽量维持每行完整性
-d 使用数字而不是字母作为后缀名
-a 指定后缀名的长度，默认为2位
```

将多个分割的文件进行合并

```shell
cat files_name_1 files_name_2 files_name_3 > files_name
```

## markdown

- markdown文件转word文件
  
  ```shell
    pandoc -o output.docx -f markdown -t docx filename.md
  ```

## find

查找具体文件    

```shell
find / -name 文件名称
```

查找指定用户的文件

```shell
find ./* -user 用户名
```

查找指定用户组的文件

```shell
find ./* -group 用户组
```



## ls

`ls -lh`以可读性G、M查看文件的大小

## 格式化json

```shell
echo '{"kind": "Service", "apiVersion": "v1", "status": {"loadBalancer": true}}'|jq .
```

## SED

- 替换字符
  
  linux环境：
  
  ```shell
  sed -i 's/Search_String/Replacement_String/g' Input_File
  ```
  
  mac环境（需要设置备份，以防文件损坏）
  
  ```shell
  sed -i .bak 's/Search_String/Replacement_String/g' Input_File
  ```

## 转换文件编码格式

首先我们来看看在 Linux 系统中如何查看文件的编码格式，可以在 vim 中使用如下命令查看：

```shell
:set fileencoding
```

输出可能是这样

```
fileencoding=utf-81
```

也可以使用 `file` 和 `identify` 命令查看。

然后使用 `iconv` 进行编码格式的转换，比如将一个 utf-8 编码的文件转换成 GBK 编码，命令如下：

```shell
$ iconv -f UTF-8 -t GBK input.file -o output.file
```

- 如果遇到]iconv: 未知xxxx处的非法输入序列,一种解决方法是加入 -c选项：忽略无效字符
  
  ```shell
  iconv -c  -f gb2312 -t utf8 test.txt -o output.file
  ```

```
iconv -f gb18030 -t UTF-8 input.file -o output.file

gb18030
```

## tar

- c – Creates a new .tar archive file.

- x — to untar or extract a tar file

- v – Verbosely show the .tar file progress.

- f – File name type of the archive file.

- z — gzip archive file

- j —  bz2 feature compress and create archive file

- t — to list the contents of tar archive file

## 加密

用zip命令对文件加密压缩和解压

```shell
zip -re filename.zip filename 
回车，输入2次密码
```

## 批量替换文件名

```bash
rename -n -e 's/待替换字符串/替换字符串/'  *.png
```

## 查找指定目录下的文件内容

```shell
grep -rn "info" *
```

## 查询大文件里面的内容

格式：

```shell
// 使用管道符可以实现过滤既满足时间又满足ip的行。
grep -n -e “10.198.2.133” prometheus.log |grep -e “2019-09-24”|head -n 3
```

参数解释：
-n 参数的作用是显示查找结果的所在行号
-e 参数表示我们需要搜索的关键字，多个关键字就用多个 -e 参数
prometheus.log 表示待搜索的大日志文件
head -n 3 表示显示前面查询结果的前三条记录

## 排除指定内容

要仅打印与搜索模式不匹配的行，可以使用grep的`-v`或`--invert-match`选项。进行反转的匹配。

```shell
grep -v xxx
```



## wget

- 下载指定目录

```bash
wget -r --no-parent http://abc.tamu.edu/projects/tzivi/repository/revisions/2/raw/tzivi/
```
