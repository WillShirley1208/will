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
  3. nmap
  ```bash
  telnet myserver.com 80 
  nc -v myserver.com 80
  nmap myserver.com 80 
  ```

- 通过进程名查看占用端口

  - 先查看进程pid	`ps -ef | grep 进程名 `
  - 再通过pid查看占用端口    `netstat -nap | grep 进程pid `

- 通过端口查看进程

  `netstat -nap | grep 端口号 `或`netstat -apn | grep 端口号`

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

- 查看内核
  - `cat /proc/version`
  - `uname -a`
- 查看linux版本
  - `lsb_release -a`
  - `cat /etc/issue`

### 磁盘相关

- 查看系统磁盘占用情况    ` df -h`

- 查看目录下文件大小        `du -sh`

### find

查找具体文件	`find / -name 文件名称`