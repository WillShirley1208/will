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