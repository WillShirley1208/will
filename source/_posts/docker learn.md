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

```shell
查找
docker search xxx
拉取
docker pull xxx
运行(容器)
docker run xxx
删除
docker rmi xxx

docker images : 列出本地镜像。

语法
docker images [OPTIONS] [REPOSITORY[:TAG]]
OPTIONS说明：
-a :列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）；
–digests :显示镜像的摘要信息；
-f :显示满足条件的镜像；
–format :指定返回值的模板文件；
–no-trunc :显示完整的镜像信息；
-q :只显示镜像ID。
```

- 导出镜像

  ```shell
  docker save $REPOSITORY:$TAG > $PATH/$NAME.tar
  # 注意$后面的变量需要替换
  ```

- 导入镜像

  ```shell
  docker load --input $PATH/$NAME.tar
  ```

- 在镜像中运行容器

  ```shell
  docker run -it -p 8899:8899 --name test $REPOSITORY:$TAG
  
  -it：表示交互式终端的容器，非启动后立刻结束的容器
  --name test：给容器取个名字，嫌麻烦可以省去
  $REPOSITORY:$TAG：容器是用哪个镜像启动的（一个容器，必须依赖一个镜像启动）
  -v 指定了宿主机上的目录用来作为docker仓库的存储位置，如果不指定的话，registry镜像重启之后会丢失已经存储在本地仓库的镜像文件
  -p hostPort:containerPort
  -v hostVolume:containerVolume 这两个地址如果不存在都会创建，一旦容器运行，两部分会完全同步
  ```

  #### volume

> Docker Image可以理解成多个只读文件叠加而成，因此Docker Image是只读的。
>
> 当我们将其运行起来，就相当于在只读的Image外包裹了一层读写层变成了容器。
>
> 当你删除容器之后，使用这个镜像重新创建一个容器，此时的镜像的只读层还和原来的一样，但是你在读写层的修改全部都会丢失（没有使用原有volume的前提下）。
>
>  docker使用volume实现数据的持久化，不仅如此volume还能帮助容器和容器之间，容器和host之间共享数据。

- 进入运行的容器

  ```shell
  docker exec -it $CONTAINER_ID /bin/bash
  ```

  - 注意

    命令中的/bin/bash是根据` docker inspect $image_id`，根据"Cmd"配置项决定的







### 把jar包程序制作成镜像

编辑dockerfile

```dockerfile
# 基础镜像
FROM  openjdk:8-jre

# 指定路径
WORKDIR /data/justMicroservice/learning-cloudalibaba

ARG JAR_FILE=nacos-provider/target/nacos-provider-0.0.1-SNAPSHOT.jar
# 复制jar文件到路径
COPY ${JAR_FILE} nacos-provider.jar
# 启动网关服务
ENTRYPOINT ["java","-jar","nacos-provider.jar"]
```

在$path的一级目录执行（比如dockerfile位置/a/b/dockerfile，那命令在a目录下执行）

```shell
docker build -t $repository -f $path/dockerfile .
```



### 容器

```shell
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
重启
docker restart $container_id
日志
docker logs $container_id
```




>
>

### Docker File vs Docker Compose

Dockerfile is what's used to create a container image, and a Compose file is what's used to deploy an instance of that image as a container.

> Compose 是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，您可以使用 YML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务。

#### Dockerfile

Dockerfile the predecessor of a container image. You build an image from a Dockerfile. A typical Dockerfile contains special build instructions, commands like `RUN`, `ADD`, `COPY`, `ENTRYPOINT`, etc.

#### Compose file

Compose files are used in two types of deployments: in the non-cluster deployment with `docker-compose` and a cluster deployment with `docker swarm`.

Compose files are used in two types of deployments: in the non-cluster deployment with `docker-compose` and a cluster deployment with `docker swarm`.

To distinguish the two types, I'm going to address the compose file responsible for cluster deployment as stack files. I'll talk about stack files in a moment.

Compose files are part of a tool called `docker-compose`. It's a client application to the docker daemon server, kind of like the `docker` CLI client, but instead of typing the whole `run` commands every time, with `docker-compose` you can re-use the same YAML file over and over again, and deploy the same container with the same configuration as you did in the first time.

It's more readable, more maintainable, more intuitive. A single compose file can contain multiple container deployment configurations.

- 执行`docker-compose up`，报错

  ```verilog
  Couldn’t connect to Docker daemon at http+docker://localhost - is it running?
  ```

  其中`docker-compose.yml`信息如下：

  ```yaml
  version: "3.7"
  services:
    web:
      build: .
      ports:
        - "5000:5000"
    redis:
      image: "redis:alpine"
  ```

  - 解决，使用sudo权限

    1. Add user to docker group (if not already added)

       ```shell
       sudo usermod -aG docker $USER
       ```

    2. create a symbolic link to /usr/bin using the following command

       ```shell
       sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
       ```

    3. Restart docker service

       ```shell
       sudo service docker restart
       ```

    4. execute

       ```shell
       sudo docker-compose up
       ```


运行docker compose up或docker compose up -d(后台运行)运行您的整个应用程序。 `注意：每次修改任一配置文件后，都要使用 docker-compose up --build 重新构建`

> 有了docker-compose，当我们想启动多个服务时，无需再一个一个进行docker run操作，而只需要编写docker-compose.yml配置文件，即可一次运行你的全部服务。

---

| 属性                   | 描述                                  |
| ---------------------- | ------------------------------------- |
| docker-compose build   | (构建yml中某个服务的镜像)             |
| docker-compose ps      | (查看已经启动的服务状态）             |
| docker-compose kill    | (停止某个服务）                       |
| docker-compose logs    | (可以查看某个服务的log）              |
| docker-compose port    | (打印绑定的public port）              |
| docker-compose pull    | (pull服务镜像)                        |
| docker-compose up      | (启动yml定义的所有服务）              |
| docker-compose stop    | (停止yml中定义的所有服务）            |
| docker-compose start   | (启动被停止的yml中的所有服务）        |
| docker-compose kill    | (强行停止yml中定义的所有服务）        |
| docker-compose rm      | （删除yml中定义的所有服务）           |
| docker-compose restart | (重启yml中定义的所有服务）            |
| docker-compose scale   | (扩展某个服务的个数，可以向上或向下） |
| docker-compose version | （查看compose的版本）                 |

日志输出  

终端输出：`docker-compose --verbose up $service_name`

或者docker-compose.yml配置

```shell
stdin_open: true
tty: true
```





---

