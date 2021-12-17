---
title: docker es
date: 2021-12-17 18:51:56
tags: snippet
categories: docker
---

> 文中hostAddress需要替换具体的ip地址

### 环境准备

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

### manual init data

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

  

### 制作elasticsearch镜像

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