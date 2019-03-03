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

## PORT 
- Check the listening ports

  Run any one of the following command:
  ```
  	sudo lsof -i -P -n | grep LISTEN 
  	sudo netstat -tulpn | grep LISTEN
  	sudo nmap -sTU -O IP-address-Here
  ```

- checking remote system tcp 80 port status
  1. Telnet
  2. nc
  3. nmap
  ```
  telnet myserver.com 80 
  nc -v myserver.com 80
  nmap myserver.com 80 
  ```

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