---
title: linux network
date: 2017-05-9 10:01:30
tags: snippet
categories: linux
---

## PORT

- Check the listening ports
  
  Run any one of the following command:
  
  ```bash
  sudo lsof -i -P -n | grep LISTEN 
  sudo netstat -tulpn | grep LISTEN
  sudo nmap -sTU -O IP-address-Here
  ```

- checking **remote system** tcp 80 port status
  
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
  
  - 先查看进程pid    `ps -ef | grep 进程名 `
  - 再通过pid查看占用端口    `netstat -nap | grep 进程pid `

- 通过端口查看进程
  
  ```shell
  netstat -an | grep <portnumber>
  lsof -i:<portnumber>
  ```

## 网络代理

- 查看本地网络代理
  
  ```
  export | grep -i proxy
  ```

- 关闭代理
  
  ```shell
  unset http_proxy 
  ```

## curl

- pretty json 

  curl 'address:port' | json_pp

## 端口远程访问

检测端口是否允许远程访问

```
netstat -an | grep <port>

查看输出结果。如果输出结果中有 0.0.0.0:<port> 或者 :::<port>，表示该端口允许远程访问；
如果输出结果中只有 127.0.0.1:<port> 或者 ::1:<port>，表示该端口只允许本地访问
```

- 在 Ubuntu 中，要开启指定端口的远程访问，需要进行以下步骤：

1. 确认端口是否已经开启：使用 `netstat` 命令检查要开启的端口是否已经在监听状态。如果已经在监听状态，则可以直接进行下一步操作；如果没有在监听状态，则需要先启动对应的服务程序。

2. 修改防火墙规则：使用 `ufw` 命令修改防火墙规则，开放指定端口。例如，要开放端口号为 80 的端口，可以使用以下命令：

   ```
   sudo ufw allow 80/tcp
   ```

   这个命令会将端口号为 80 的端口加入到防火墙规则中，并允许 TCP 协议通过该端口进行访问。

3. 重启防火墙服务：在修改防火墙规则后，需要重启防火墙服务才能使修改生效。可以使用以下命令重启防火墙服务：

   ```
   sudo service ufw restart
   ```

4. 确认防火墙规则是否正确：使用以下命令查看当前的防火墙规则：

   ```
   sudo ufw status numbered
   ```

## 根据PID查看子进程

```
netstat -lntp |grep PID
```

