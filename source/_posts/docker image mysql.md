---
title: docker image mysql
date: 2021-12-17 18:57:57
tags: snippet
categories: docker
---

## mysql镜像制作

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