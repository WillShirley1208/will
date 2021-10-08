---
title: docker learn
date: 2019-10-08 17:09:09
tags: learn
categories: docker
---



#### Docker 包括三个基本概念:

- **镜像（Image）**：Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。
- **容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
- **仓库（Repository）**：仓库可看着一个代码控制中心，用来保存镜像。

### 操作镜像

```
查找
docker search xxx
拉取
docker pull xxx
运行(容器)
docker run xxx
删除
docker rmi xxx
```



### 操作容器

```
查看正在运行
docker ps
查看所有
docker ps -a
启动
docker start 容器名或容器id
终止
docker stop [NAME]/[CONTAINER ID]:将容器退出。
docker kill [NAME]/[CONTAINER ID]:强制停止一个容器。

查看容器端口
docker port 容器名或容器id


删除
docker rm -f 容器id
导出
docker export 容器id > xxx.tar
导入
docker import - test/xxx:v1
```
### mysql

- 密码123456

- 创建容器

  ```
  docker run --name mysql-server -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
  注意：
  -d:让容器在后台运行
  -P(大写):是容器内部端口随机映射到主机的高端口
  -p(小写):是容器内部端口绑定到指定的主机端口
  ```

- 进入容器

  ```shell
  docker exec -it mysql-server /bin/bash
  ```

- 访问

  `docker exec -it mysql-server mysql -uroot -p`

- 修改root 可以通过任何客户端连接

  ```shell
   ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
  ```

- 从外部访问docker mysql-server

  ```shell
  mysql -h127.0.0.1 -P3306 -uroot -p
  ```

- 导入sql文件

  ```
  先将文件导入到容器
  #docker cp **.sql 容器名:/root/
  进入容器
  #docker exec -ti 容器名或ID sh
  登录数据库
  # mysql -uroot -p 
  将文件导入数据库
  source 数据库名 < /root/***.sql
  ```

- 导出数据库

  ```shell
  docker exec -it  mysql-server（容器名） mysqldump -uroot -p123456 数据库名称 > /opt/sql_bak/test_db.sql（导出表格路径）
  ```

  

### kafka

### ignite

### es

### flink

### 配置镜像源安装 VIM

```shell
mv /etc/apt/sources.list /etc/apt/sources.list.bak 
echo "deb http://mirrors.163.com/debian/ jessie main non-free contrib" >/etc/apt/sources.list 
echo "deb http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list 
echo "deb-src http://mirrors.163.com/debian/ jessie main non-free contrib" >>/etc/apt/sources.list 
echo "deb-src http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list 
#更新安装源 
apt-get update 
#如果下载过程中卡在[waiting for headers] 删除/var/cache/apt/archives/下的所有文件 
#安装vim 
apt-get install vim
```





#### portainer

- 密码重置

  - 下载帮助镜像portainer/helper-reset-password
    docker pull portainer/helper-reset-password
  - 停止运行的portainer
    docker stop "id-portainer-container"
  - 运行重置命令
    docker run --rm -v portainer_data:/data portainer/helper-reset-password
  - 结果
    2020/06/04 00:13:58 Password successfully updated for user: admin
    2020/06/04 00:13:58 Use the following password to login: &_4#\3^5V8vLTd)E"NWiJBs26G*9HPl1
  - 重新运行portainer,密码 为👆重置的 &_4#\3^5V8vLTd)E"NWiJBs26G*9HPl1
    docker start "id-portainer-container"
- 现在密码为 admin/admin



#### 修改已有容器的端口映射

1. 停止容器 

2. 停止docker服务(systemctl stop docker) 

3. 修改这个容器的hostconfig.json文件中的端口（原帖有人提到，如果config.v2.json里面也记录了端口，也要修改）

   ```
   cd /var/lib/docker/3b6ef264a040* #这里是CONTAINER ID
   vi hostconfig.json
   如果之前没有端口映射, 应该有这样的一段:
   "PortBindings":{}
   增加一个映射, 这样写:
   "PortBindings":{"3306/tcp":[{"HostIp":"","HostPort":"3307"}]}
   前一个数字是容器端口, 后一个是宿主机端口. 
   而修改现有端口映射更简单, 把端口号改掉就行.
   ```

4. 启动docker服务(systemctl start docker) 

5. 启动容器