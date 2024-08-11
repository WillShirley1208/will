---
title: Kubernetes learn
date: 2021-12-02 16:57:50
tags: learn
categories: Kubernetes
---

> **容器是一个“单进程”模型**
>
> 一个“容器”，实际上是一个由 Linux Namespace、Linux Cgroups 和 rootfs 三种技术构建出来的进程的隔离环境
>
> 一个正在运行的 Linux 容器，其实可以被“一分为二”地看待：
>
> 1. 一组联合挂载在 /var/lib/docker/aufs/mnt 上的 rootfs，这一部分我们称为“容器镜像”（Container Image），是容器的静态视图；
> 2. 一个由 Namespace+Cgroups 构成的隔离环境，这一部分我们称为“容器运行时”（Container Runtime），是容器的动态视图。

# define

> 容器本身没有价值，有价值的是“容器编排”。

Kubernetes is pronounced **coo-ber-net-ees**, not coo-ber-neats. People also use the shortened version k8s a lot. Please don’t pronounce that one k-eights—it is still coo-ber-net-ees.

### Difference between Docker and Kubernetes

> Docker is a containerization platform, and Kubernetes is a container orchestrator for container platforms like Docker. 

### Docker Container

- 默认情况下，Docker 会为你提供一个隐含的 ENTRYPOINT，即：/bin/sh -c。所以，在不指定 ENTRYPOINT 时，比如运行在容器里的完整进程是：/bin/sh -c “python xxx.py”，即 CMD 的内容就是 ENTRYPOINT 的参数

### Kubernetes architecture and its components

We can break down the components into three main parts.

1. The Control Plane - The Master.
2. Nodes - Where pods get scheduled.
3. Pods - Holds containers.

![](./img/kubernetes_cluster.png)

Docker is a platform and tool for building, distributing, and running Docker containers. It offers its own native clustering tool that can be used to orchestrate and schedule containers on machine clusters. Kubernetes is a container orchestration system for Docker containers that is more extensive than Docker Swarm and is meant to coordinate clusters of nodes at scale in production in an efficient manner. It works around the concept of pods, which are scheduling units (and can contain one or more containers) in the Kubernetes ecosystem, and they are distributed among nodes to provide high availability. One can easily run a Docker build on a Kubernetes cluster, but Kubernetes itself is not a complete solution and is meant to include custom plugins.

# concept

## cgoups

>  **Linux Cgroups 的全称是 Linux Control Group。它最主要的作用，就是限制一个进程组能够使用的资源上限，包括 CPU、内存、磁盘、网络带宽等等。**

- Cgroups 给用户暴露出来的操作接口是文件系统，即它以文件和目录的方式组织在操作系统的 /sys/fs/cgroup 路径下
- Linux Cgroups 的设计还是比较易用的，简单粗暴地理解它就是一个子系统目录加上一组资源限制文件的组合

## rootfs

> 它只是一个操作系统的所有文件和目录，并不包含内核，最多也就几百兆

# command

- 需要切换到root用户
- 或使用kubeconfig `kubectl delete pod auth-server-server-5cbfd46c5b-9zs26 --force --kubeconfig=./kubeconfig`

## 查看所有 namespace

```bash
kubectl get namespaces
```



## 查看运行程序

```shell
kubectl get pod -n 命令空间
```



## 查看日志

```shell
kubectl logs -f 容器id -n 命令空间
```

## 查看容器启动配置

```
kubectl get pod 容器id --kubeconfig=/path/to/configfile -o yaml > env-vq48.yaml
```

## 进入容器

- without kubeconfig

```shell
kubectl exec -it  容器id -n 命令空间 -c entity-server-server -- sh
```

- With kubeconfig

```shell
kubectl --kubeconfig=./kubeconfig-vq48 exec -it auth-server-server-687cf494cc-ff4s4 -- sh
```



## 复制服务器文件到宿主机

```shell
kubectl cp 命令空间/容器id:/path/to/source_file ./path/to/local_file
```

# conclusion

- 过去很多的集群管理项目（比如 Yarn、Mesos，以及 Swarm）所擅长的，都是把一个容器，按照某种规则，放置在某个最佳节点上运行起来。这种功能，我们称为“调度”。

  而 Kubernetes 项目所擅长的，是按照用户的意愿和整个系统的规则，完全自动化地处理好容器之间的各种关系。**这种功能，就是：编排。**
