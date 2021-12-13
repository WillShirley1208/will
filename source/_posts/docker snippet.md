---
title: docker snippet
date: 2019-08-16 14:34:30
tags: snippet
categories: docker
---



## Manage Docker as a non-root user

1. Create the `docker` group.

   ```
   $ sudo groupadd docker
   ```

2. Add your user to the `docker` group.

   ```
   $ sudo usermod -aG docker $USER
   ```

3. Log out and log back in so that your group membership is re-evaluated.

   If testing on a virtual machine, it may be necessary to restart the virtual machine for changes to take effect.

   On a desktop Linux environment such as X Windows, log out of your session completely and then log back in.

   On Linux, you can also run the following command to activate the changes to groups:

   ```
   $ newgrp docker 
   ```

4. Verify that you can run `docker` commands without `sudo`.

   ```
   $ docker run hello-world
   ```



### docker compose

#### Exit Codes

Common exit codes associated with docker containers are:

- **Exit Code 0**: Absence of an attached foreground process
- **Exit Code 1**: Indicates failure due to application error
- **Exit Code 137**: Indicates failure as container received SIGKILL (Manual intervention or ‘oom-killer’ [OUT-OF-MEMORY])
- **Exit Code 139**: Indicates failure as container received SIGSEGV
- **Exit Code 143**: Indicates failure as container received SIGTERM

- **Exit Code 126**: Permission problem or command is not executable
- **Exit Code 127**: Possible typos in shell script with unrecognizable characters





### 镜像重新编译

如果修改了 Dockerfile内容里面相关的信息，需要重新编译镜像，如果使用docker compose，则需要使用命令

```shell
docker-compose up --build
```



### 后台运行

```shell
docker-compose up -d
```





### 查看container现在工作网络模式

- 列出docker的所有网络模式

  ```shell
  docker network ls
  ```

- 针对bridge和host分别查找有哪些container在其中

  ```shell
  docker network inspect bridge
  docker network inspect host
  ```

- 直接查看container的信息，找到network段查看。或者用grep筛选出network。

  ```shell
  docker inspect 容器名/容器ID
  docker inspect 容器名/容器ID | grep -i “network” # 其中grep的“-i”表示不区分大小写。
  ```






### Solution

#### Failed to load listeners: no sockets found via socket activation: make sure the service was started by systemd

```
edit the docker’s systemd unit file and change the ExecStart:
sudo systemctl edit docker.service

edite The value ExecStart, change fd:// to unix://:
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H unix://
```

#### Error creating default "bridge" network: cannot create network (docker0): conflicts with network (docker0): networks have same bridge name

```shell
sudo rm -rf /var/lib/docker/network
sudo systemctl start docker
```

