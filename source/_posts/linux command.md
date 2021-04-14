---
title: linux command
date: 2015-10-16 12:01:30
tags: command
categories: linux
---

[Bash Scripting Tutorial for Beginners](https://linuxconfig.org/bash-scripting-tutorial-for-beginners)

### Bash Shell Scripting Definition
- Bash:Bourne-Again SHell
  Bash is a command language interpreter. 
## conclusion
Do not be afraid to break things as that is perfectly normal. Troubleshooting and fixing code is perhaps the best booster for you to enhance your understanding of bash scripting and to improve your ability.

[Bash scripting Tutorial](https://linuxconfig.org/bash-scripting-tutorial#h24-stdout-to-screen)

## session

- kill seesion `screen -X -S [session # you want to kill] quit`
- 新建screen会话           screen -S xxx
- 恢复指定会话               screen -r xxx
- 查看所有会话                screen -ls
- 删除指定会话                screen -S xxx -X quit
- 回到终端                        Ctrl-a d

## PORT 
- Check the listening ports

  Run any one of the following command:
  ```bash
  sudo lsof -i -P -n | grep LISTEN 
  sudo netstat -tulpn | grep LISTEN
  sudo nmap -sTU -O IP-address-Here
  ```

- checking remote system tcp 80 port status
  1. Telnet

  2. nc

     ```
      nc -zvw10 192.168.0.1 22
     其中参数：
     z: zero-I/O mode which is used for scanning
     v: for verbose output
     w10: timeout wait seconds
     ```

     

  3. nmap
  ```bash
  telnet myserver.com 80 
  nc -v myserver.com 80
  nc -vn 192.168.40.146 2424
  nmap myserver.com 80 
  ```

- 通过进程名查看占用端口

  - 先查看进程pid	`ps -ef | grep 进程名 `
  - 再通过pid查看占用端口    `netstat -nap | grep 进程pid `

- 通过端口查看进程

  `netstat -nap | grep 端口号 `或`netstat -apn | grep 端口号`

  `netstat -tln | grep 8080` 查看端口8080的使用情况

## tar

- c – Creates a new .tar archive file.
- x — to untar or extract a tar file

- v – Verbosely show the .tar file progress.
- f – File name type of the archive file.

- z — gzip archive file
- j —  bz2 feature compress and create archive file
- t — to list the contents of tar archive file

## firewall

- check status : `sudo ufw status`
- enable firewall: 
```bash
$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
```
- disable firewall
```bash
$ sudo ufw disable
Firewall stopped and disabled on system startup
```

### 查看系统配置

- 查看系统
  - `cat /etc/os-release`

- 查看内核
  - `cat /proc/version`
  - `uname -a`
- 查看linux版本
  - `lsb_release -a`
  - `cat /etc/issue`

### 磁盘相关

- 查看系统磁盘占用情况    ` df -h`

- 查看目录下文件大小        `du -sh`

- 查看当前目录下一级子文件和子目录占用的磁盘容量: `du -lh --max-depth=1 `

  ```
  查看当前目录下user目录的大小，并不想看其他目录以及其子目录：
  du -sh user
  -s表示总结的意思，即只列出一个总结的值
  du -h --max-depth=0 user
  --max-depth=n表示只深入到第n层目录，此处设置为0，即表示不深入到子目录。
  ```

  

  

### find

查找具体文件	`find / -name 文件名称`

### ls

`ls -lh`以可读性G、M查看文件的大小

### history

查看历史命令，支持 grep过滤操作

### 卸载安装的软件

```shell
浏览已安装的程序	dpkg --list
卸载程序和所有配置文件	sudo apt-get --purge remove <programname>
只卸载程序	sudo apt-get remove <programname>
```

### 文件权限

```
r=4，w=2，x=1
若要rwx属性则4+2+1=7；
若要rw-属性则4+2=6；
若要r-x属性则4+1=7
```

###　安装deb文件出错时

使用`apt-get -f -y install`修复之后，再进行安装

```
1.执行命令sudo dpkg　-i　XXX.deb　返回依赖关系错误提示
2.执行sudo apt-get -f install　这条命令将自动安装需要的依赖包．
3.再次执行命令sudo dpkg　-i　XXX.deb　安装成功
```

## 光标

```
Ctrl+a：光标回到命令行首。 （a：ahead）
Ctrl+e：光标回到命令行尾。 （e：end）
Ctrl+b：光标向行首移动一个字符。 （b：backwards）
Ctrl+ f：光标向行尾移动一个字符。 （f：forwards）
Ctrl+w: 删除光标处到行首的字符。
Ctrl+k：删除光标处到行尾的字符。
Ctrl+u：删除整个命令行文本字符。
Ctrl+h：向行首删除一个字符。
Ctrl+d：向行尾删除一个字符。

Ctrl + xx ：在命令行尾和光标之间移动
```

### AWK

- `awk -v FS="输入分隔符" -v OFS='输出分隔符' '{if($1==$5) print $1,$5,$10} filename'`

  查找filename文件（文件中列的分隔符为“输入分隔符”）中，每一行第一列和第五列相等的行，并输出第一列、第五列、第十列，切输出字段分隔符为“输出分隔符”。如果不配置FS和OFS，那么输入输出分隔符均默认为空

- [exclude a column with awk](https://www.commandlinefu.com/commands/view/6872/exclude-a-column-with-awk), 比如打印除第5列的其它所有列

  awk '{ $5=""; print }' file

### 统计文件行数

语法：wc [选项] 文件…

说明：该命令统计给定文件中的字节数、字数、行数。如果没有给出文件名，则从标准输入读取。wc同时也给出所有指定文件的总统计数。字是由空格字符区分开的最大字符串。

该命令各选项含义如下：

　　- c 统计字节数。

　　- l 统计行数。

　　- w 统计字数。

这些选项可以组合使用。

### 权限

使文件可以直接执行的命令：chmod +x filename

使所有用户对目录都有读写权限：sudo chmod ugo+rw /opt

### 时区

[CentOS 7 时区设置](https://www.cnblogs.com/zhangeamon/p/5500744.html)

### grep

限定查询结果之后的前几行 `grep -m 10 <pattern> <file> `

限定查询结果倒数的几行 `grep <pattern> <file> | tail -10`

### 日期

- 判断　day of year 	

  `doy=$(date +%j)`

- 制定日期减一天

  `date -d"20140101 -1 days" +"%Y%m%d"`

### 剪切板

将剪切板中的内容输出到文件 	echo $(xsel --clipboard) >> a.txt 

将文件的内容复制到剪切板 		cat a.txt | xsel --clipboard

#### securtCRT

```
下载服务器文件	sz filename
上传本地文件 rz filename
```

#### 格式化json

```shell
echo '{"kind": "Service", "apiVersion": "v1", "status": {"loadBalancer": true}}'|jq .
```

#### SED

- 替换字符

  ```shell
  sed -i 's/Search_String/Replacement_String/g' Input_File
  ```

#### 转换文件编码格式

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

  

  iconv -f gb18030 -t UTF-8 input.file -o output.file

  gb18030



### tr命令

tr -- translate or delete characters 

- 大小写转换

  ```shell
  cat file | tr A-Z a-z 
  cat file | tr a-z A-Z
  ```