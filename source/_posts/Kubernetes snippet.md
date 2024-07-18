---
title: Kubernetes learn
date: 2021-12-02 16:57:50
tags: learn
categories: Kubernetes
---

Kubernetes is pronounced **coo-ber-net-ees**, not coo-ber-neats. People also use the shortened version k8s a lot. Please don’t pronounce that one k-eights—it is still coo-ber-net-ees.

### Difference between Docker and Kubernetes

> Docker is a containerization platform, and Kubernetes is a container orchestrator for container platforms like Docker. 

### Docker Container Problems:

- How would all of these containers be coordinated and scheduled? 
- How do you seamlessly upgrade an application without any interruption of service? 
- How do you monitor the health of an application, know when something goes wrong and seamlessly restart it? 

When most people talk about “Kubernetes vs. Docker,” what they really mean is “Kubernetes vs. Docker Swarm.” 

### Kubernetes architecture and its components

We can break down the components into three main parts.

1. The Control Plane - The Master.
2. Nodes - Where pods get scheduled.
3. Pods - Holds containers.

![](./img/kubernetes_cluster.png)

Docker is a platform and tool for building, distributing, and running Docker containers. It offers its own native clustering tool that can be used to orchestrate and schedule containers on machine clusters. Kubernetes is a container orchestration system for Docker containers that is more extensive than Docker Swarm and is meant to coordinate clusters of nodes at scale in production in an efficient manner. It works around the concept of pods, which are scheduling units (and can contain one or more containers) in the Kubernetes ecosystem, and they are distributed among nodes to provide high availability. One can easily run a Docker build on a Kubernetes cluster, but Kubernetes itself is not a complete solution and is meant to include custom plugins.



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

