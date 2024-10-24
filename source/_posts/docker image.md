---
title: docker image
date: 2021-12-17 18:51:56
tags: image
categories: docker
---

# concept

- **镜像（Image）**：Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。
- **容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
- **仓库（Repository）**：仓库可看着一个代码控制中心，用来保存镜像。

## operate

### command

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
  docker save -o <output-file>.tar <image-name>
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
> docker使用volume实现数据的持久化，不仅如此volume还能帮助容器和容器之间，容器和host之间共享数据。

- 进入运行的容器

  ```shell
  docker exec -it $CONTAINER_ID /bin/bash
  ```

  - 注意

    命令中的/bin/bash是根据` docker inspect $image_id`，根据"Cmd"配置项决定的



### jar -> image

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



## archive artifact

```shell
# step1
docker save -o /tmp/my_app_image.tar my_app:latest

# step2 save_image.tar is equal my_app_image.tar
docker load -i /path/to/destination/save_immy_app_image.tarage.tar
```



---

# advanced

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



# es

> 文中hostAddress需要替换具体的ip地址

## 环境准备

> 查看现有环境相关参数ulimit -a

- 设置文件句柄数，在**/etc/security/limits.conf**中设置

  ```
  # End of file
  * hard nofile 65536
  * soft nofile 65536
  ```

- 修改max user processes进程数，在**/etc/security/limits.conf**中设置

  ```
  * soft nproc 65536
  * hard nproc 65536
  ```

- 调整vm.max_map_count的大小，该参数会限制一个进程可以拥有的VMA(虚拟内存区域)的数量

  通过修改**/etc/sysctl.conf**参数

  ```
  vm.max_map_count=655360
  ```

  然后执行`sysctl -p`

- 调整stack size的大小（可选），在**/etc/security/limits.conf**中设置

  ```
  * soft stack 1024
  * hard stack 1024
  ```

## manual init data

- create index

  ```shell
  curl -H 'Content-Type: application/json' -d '@/data/es_mapping.json' -X PUT 'http://localhost:9200/indexName'
  ```

- import data

  ```shell
  curl -H 'Content-Type: application/json' --data-binary '@/data/es_init_data.txt' 'http://localhost:9200/_bulk'
  ```


- 拉取远程仓库镜像文件

  ```shell
  docker pull hostAddress/dlabel/service:elasticsearch
  ```

- 启动容器

  ```shell
  sudo docker run -d --name es_origin -e ES_JAVA_POTS="-Xms6g -Xmx6g" -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.16.1
  ```


## 镜像制作

1. 导出ES的已有索引和数据

- 环境准备

  - 安装nodejs，安装文件地址[nodejs](<https://nodejs.org/en/download/>)
  - 安装elasticdump，安装命令`npm install -g elasticdump`

- 导出es索引文件**es_mapping.json**

  ```shell
  /$nodejs_home/lib/node_modules/elasticdump/bin/elasticdump \               --input=http://127.0.0.1:9200/indexName \
    --output=/data/es_mapping.json \
    --type=mapping
  ```

  > 注意：$nodejs_home代表nodejs的安装目录

- 导出es数据**es_init_data.txt**

  ```shell
  /$nodejs_home/lib/node_modules/elasticdump/bin/elasticdump \           
    --input=http://127.0.0.1:9200/indexName \
    --output=/data/es_init_data.txt \
    --searchBody '{"query":{"match_all":{ }}}'
  ```

2. 编写es数据初始化脚本 **initEs.sh**

   ```shell
   #create index
   curl -H 'Content-Type: application/json' -d '@/data/es_mapping.json' -X PUT 'http://127.0.0.1:9200/indexName'
   #import data
   curl -H 'Content-Type: application/json' --data-binary '@/data/es_init_data.txt' 'http://127.0.0.1:9200/_bulk'
   ```

   > initEs.sh文件同1,2操作中的文件存放路径保持一致，均放在/data目录下

3. 在/data目录下创建Dockerfile文件

   ```shell
   FROM elasticsearch:7.16.1
   COPY ./data/* /data/
   RUN chown -R elasticsearch:root /data 
   USER elasticsearch
   RUN elasticsearch -E discovery.type=single-node -p /tmp/epid & /bin/bash /data/wait-for-it.sh -t 0 localhost:9200 -- /data/initEs.sh; kill $(cat /tmp/epid) && wait $(cat /tmp/epid); exit 0;
   ```

   

4. 创建镜像

   ```shell
   docker build -t dlabel:elasticsearch .
   ```

   > dlabel:es是 REPOSITORY:TAG格式，可自行更改

5. 登录远程仓库

   ```shell
   docker login hostAddress
   ```

   根据提示，输入用户名admin，密码Harbor12345

6. 映射远程仓库REPOSITORY:TAG

   ```shell
   docker image tag dlabel:elasticsearch hostAddress/dlabel/service:elasticsearch
   ```

   > 其中dlabel:elasticsearch和操作3中保持一致
   >

7. 推送当地镜像到远程仓库

   ```shell
   docker push hostAddress/dlabel/service:elasticsearch
   ```

# mysql

## 镜像制作

1. 需要备份当前需要同步的全量数据

   ```shell
   docker exec -it dlabel_mysql mysqldump -uroot -p123456 dls > /path/to/backup.sql
   ```

   > 注意事项：
   >
   > 其中dlabel_mysql，是在第二步中设置的name的名称
   >
   > /path/to/backup.sql是导出sql的地址路径，根据操作系统不同，需要自行更改
   >
   > 假定以下操作是在/path/to的目录下



2. 在/path/to目录下创建Dockerfile文件

   ```shell
   # Derived from official mysql image (our base image)
   FROM mysql:5.7.30
   # Add the content of the sql-scripts/ directory to your image
   # All scripts in docker-entrypoint-initdb.d/ are automatically
   # executed during container startup
   COPY ./backup.sql /docker-entrypoint-initdb.d/
   ```

   > 注意COPY指令中，backup.sql需要和操作1中的导出文件名保持一致

3. 创建镜像

   ```shell
   docker build -t dlabel:mysql20211216 .
   ```

   > dlabel:mysql20211216是 REPOSITORY:TAG格式，可自行更改

4. 登录远程仓库

   ```shell
   docker login hostAddress
   ```

   根据提示，输入用户名admin，密码Harbor12345

5. 映射远程仓库REPOSITORY:TAG

   ```shell
   docker image tag dlabel:mysql20211216 hostAddress/dlabel/service:mysql20211216
   ```

   > 其中dlabel:mysql20211216和操作3中保持一致
   >
   > hostAddress/dlabel/service:mysql20211216，格式为hostAddress/library/REPOSITORY:TAG，其中可自行修改service:mysql20211216名称

6. 推送当地镜像到远程仓库

   ```shell
   docker push hostAddress/dlabel/service:mysql20211216
   ```

7. 登录**http://hostAddress**查看镜像上传情况

8. 在镜像详情界面，点击“拉取命名”按钮进行命令复制，在终端执行命令即可拉取该镜像