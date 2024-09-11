---
title: docker image
date: 2019-08-16 14:34:30
tags: snippet
categories: docker
---

#### Docker 包括三个基本概念:

- **镜像（Image）**：Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。
- **容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
- **仓库（Repository）**：仓库可看着一个代码控制中心，用来保存镜像。

### 操作镜像

```shell
# 构建 （--no-cache 可以清理缓存进行构建）
docker build --no-cache -t your-image-name .

# 查找
docker search xxx

# 拉取
docker pull xxx

# 运行(容器)
docker run xxx

# 删除
docker rmi xxx

# 删除所有tag为 none 的镜像
sudo docker images -q --filter "dangling=true" | sudo xargs docker rmi

docker images : 列出本地镜像。

# 语法
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

- step1: 

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

- Step2: 

  在$path的一级目录执行（比如dockerfile位置/a/b/dockerfile，那命令在a目录下执行）

```shell
docker build -t $repository -f $path/dockerfile .
```



---

## add git commit info to image tag

```shell
#!/bin/sh

IMAGE_NAME=your-image-name
echo "image name: $IMAGE_NAME"

GIT_BRANCH=$(git symbolic-ref --short HEAD)
LAST_COMMIT=$(git rev-parse HEAD)
echo "git commit info : $LAST_COMMIT"

docker build . -t $IMAGE_NAME:"$GIT_BRANCH-$LAST_COMMIT"
```

