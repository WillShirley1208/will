---
title: docker compose
date: 2021-12-17 11:21:10
tags: snippet
categories: docker
---

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





### 镜像重新编译

如果修改了 Dockerfile内容里面相关的信息，需要重新编译镜像，如果使用docker compose，则需要使用命令

```shell
docker-compose up --build
```



### 后台运行

```shell
docker-compose up -d
```