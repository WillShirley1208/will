---
title: Kubernetes learn
date: 2024-08-05 16:57:50
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

> Docker is a containerization platform, and Kubernetes is a container orchestrator for container platforms like Docker. 



### architecture

We can break down the components into three main parts.

1. The Control Plane - The Master.
2. Nodes - Where pods get scheduled.
3. Pods - Holds containers.

![](/images/k8s/k8s-architecture-overview.png)

# concept

> kubernetes设计思想**“一切皆对象”**：应用是 Pod 对象，应用的配置是 ConfigMap 对象，应用要访问的密码则是 Secret 对象。
>
> **声明式 API，是 Kubernetes 项目编排能力“赖以生存”的核心所在**

## dependency

> Namespace 做隔离，Cgroups 做限制，rootfs 做文件系统

### namespace

TBD

### cgoups

>  **Linux Cgroups 的全称是 Linux Control Group。它最主要的作用，就是限制一个进程组能够使用的资源上限，包括 CPU、内存、磁盘、网络带宽等等。**

- Cgroups 给用户暴露出来的操作接口是文件系统，即它以文件和目录的方式组织在操作系统的 /sys/fs/cgroup 路径下
- Linux Cgroups 的设计还是比较易用的，简单粗暴地理解它就是一个子系统目录加上一组资源限制文件的组合

### rootfs

> 它只是一个操作系统的所有文件和目录，并不包含内核，最多也就几百兆



## Kubelet



## CRI

> **Container Runtime Interface** 容器运行时接口
>
> kubelet 调用下层容器运行时的执行过程，并不会直接调用 Docker 的 API，而是通过CRI的 gRPC 接口来间接执行



## API object

> Kubernetes API 对象，往往由 Metadata 和 Spec 两部分组成
>
> 在 Kubernetes 项目中，一个 API 对象在 Etcd 里的完整资源路径，是由：Group（API 组）、Version（API 版本）和 Resource（API 资源类型）三个部分组成的。

```yaml
# “CronJob”就是这个 API 对象的资源类型（Resource），“batch”就是它的组（Group），v2alpha1 就是它的版本（Version）。
apiVersion: batch/v2alpha1
kind: CronJob
...
```



### Secret

- create

  ```shell
  kubectl create secret generic user --from-file=./username.txt
  ```

- check

  ```shell
  kubectl get secrets
  ```

  

### ConfigMap

- create

  ```shell
  kubectl create configmap ui-config --from-file=example/ui.properties
  ```

- get yaml

  ```shell
  kubectl get configmaps ui-config -o yaml
  ```



### StatefulSet

> StatefulSet 的核心功能，就是通过某种方式记录这些状态，然后在 Pod 被重新创建时，能够为新 Pod 恢复这些状态

#### 拓扑状态

- Headless Service

  > 通过 Headless Service 的方式，StatefulSet 为每个 Pod 创建了一个固定并且稳定的 DNS 记录，来作为它的访问入口。

  ```
  <pod-name>.<svc-name>.<namespace>.svc.cluster.local
  ```

#### 存储状态

> Kubernetes 中 PVC 和 PV 的设计，**实际上类似于“接口”和“实现”的思想**。

- **Persistent Volume Claim(PVC)**
- **persistent Volume(PV)**

### DaemonSet

1. 这个 Pod 运行在 Kubernetes 集群里的每一个节点（Node）上；
2. 每个节点上只有一个这样的 Pod 实例；
3. 当有新的节点加入 Kubernetes 集群后，该 Pod 会自动地在新节点上被创建出来；而当旧节点被删除后，它上面的 Pod 也相应地会被回收掉。

### ControllerRevision

### Job

> Batch job

### CronJob





## pod

> Kubernetes 里“最小”的 API 对象是 Pod。Pod 可以等价为一个应用，所以，Pod 可以由多个紧密协作的容器组成
>
> **Pod 就是 Kubernetes 世界里的“应用”；而一个应用，可以由多个容器组成**
>
> Pod，其实是一组共享了某些资源的容器
>
> **凡是调度、网络、存储，以及安全相关的属性，基本上是 Pod 级别的**；
>
> **凡是跟容器的 Linux Namespace 相关的属性，也一定是 Pod 级别的**
>
> **凡是 Pod 中的容器要共享宿主机的 Namespace，也一定是 Pod 级别的定义**
>
> **Pod 里的所有容器，共享的是同一个 Network Namespace，并且可以声明共享同一个 Volume**
>
> 
>
> Pod 可以被看成传统环境里的“机器”、把容器看作是运行在这个“机器”里的“用户程序”
>
> 容器，就是未来云计算系统中的进程；容器镜像就是这个系统里的“.exe”安装包

### infra container

> 在 Kubernetes 项目里，Pod 的实现需要使用一个中间容器，这个容器叫作 Infra 容器。在这个 Pod 中，Infra 容器永远都是第一个被创建的容器，而其他用户定义的容器，则通过 Join Network Namespace 的方式，与 Infra 容器关联在一起。
>
> 在 Kubernetes 项目里，Infra 容器一定要占用极少的资源，所以它使用的是一个非常特殊的镜像，叫作：`k8s.gcr.io/pause`。这个镜像是一个用汇编语言编写的、永远处于“暂停”状态的容器，解压后的大小也只有 100~200 KB 左右。

### config

- Metadata
- Spec
- 

### stragety

- **ImagePullPolicy**

  ```
  Always Never IfNotPresent
  ```

- **Lifecycle**

  ```
  postStart preStop
  ```

- **restartPolicy**

  ```
  Always：在任何情况下，只要容器不在运行状态，就自动重启容器；
  OnFailure: 只在容器异常时才自动重启容器；
  Never: 从来不重启容器。
  ```

- **PodPreset**

  ```
  PodPreset 里定义的内容，只会在 Pod API 对象被创建之前追加在这个对象本身上，而不会影响任何 Pod 的控制器的定义
  ```

  

- **Status**

  ```
  pending running succeeded failed upknown
  ```

  

## Volume

> 所谓容器的 Volume，其实就是将一个宿主机上的目录，跟一个容器里的目录绑定挂载在了一起
>
> 所谓的“持久化 Volume”，指的就是这个宿主机上的目录，具备“持久性”。即这个目录里面的内容，既不会因为容器的删除而被清理掉，也不会跟当前的宿主机绑定。

**“持久化”宿主机目录的过程，可以形象地称为“两阶段处理”**

- 第一阶段 Attach：虚拟机挂载远程磁盘的操作
- 第二阶段 Mount：将磁盘设备格式化并挂载到 Volume 宿主机目录的操作

### Dynamic Provisioning

> 自动创建 PV 的机制

### PersistentVolumeController

### StorageClass

- 定义

  ```
  第一，PV 的属性。比如，存储类型、Volume 的大小等等。
  第二，创建这种 PV 需要用到的存储插件。比如，Ceph 等等。
  ```

- 充当 PV 的模板。并且，只有同属于一个 StorageClass 的 PV 和 PVC，才可以绑定在一起

### csi

> Container Storage Interface **容器的持久化存储的含义**，就是用来保存容器存储状态的重要手段：存储插件会在容器里挂载一个基于网络或者其他机制的远程数据卷，使得在容器里创建的文件，实际上是保存在远程存储服务器上，或者以分布式的方式保存在多个节点上，而与当前宿主机没有任何绑定关系。这样，无论你在其他哪个宿主机上启动新的容器，都可以请求挂载指定的持久化存储卷，从而访问到数据卷里保存的内容。
>
> **CSI 插件体系的设计思想，就是把 Provision 阶段，以及 Kubernetes 里的一部分存储管理功能(比如“Attach 阶段”和“Mount 阶段”的具体操作)，从主干代码里剥离出来，做成了几个单独的组件**

一个 CSI 插件只有一个二进制文件，但它会以 gRPC 的方式对外提供三个服务（gRPC Service），分别叫作：CSI Identity、CSI Controller 和 CSI Node。

**CSI 的设计思想**，把插件的职责从“两阶段处理”，扩展成了 Provision、Attach 和 Mount 三个阶段。其中，Provision 等价于“创建磁盘”，Attach 等价于“挂载磁盘到虚拟机”，Mount 等价于“将该磁盘格式化后，挂载在 Volume 的宿主机目录上”。

<img src="/images/k8s/csi idea.png" style="zoom:50%;"/>

#### External Components

- **Driver Registrar**

  **负责将插件注册到 kubelet 里面**（这可以类比为，将可执行文件放在插件目录下）。而在具体实现上，Driver Registrar 需要请求 CSI 插件的 Identity 服务来获取插件信息。

- **External Provisioner**

  **负责 Provision 阶段**。在具体实现上，External Provisioner 监听（Watch）了 APIServer 里的 PVC 对象。当一个 PVC 被创建时，它就会调用 CSI Controller 的 CreateVolume 方法，创建对应 PV

- **External Attacher**

  **负责“Attach 阶段”**。在具体实现上，它监听了 APIServer 里 VolumeAttachment 对象的变化。VolumeAttachment 对象是 Kubernetes 确认一个 Volume 可以进入“Attach 阶段”的重要标志；

  一旦出现了 VolumeAttachment 对象，External Attacher 就会调用 CSI Controller 服务的 ControllerPublish 方法，完成它所对应的 Volume 的 Attach 阶段。

#### Custom Components

- **CSI Identity**

  **负责对外暴露这个插件本身的信息**

- **CSI Controller**

  **定义的则是对 CSI Volume（对应 Kubernetes 里的 PV）的管理接口**，比如：创建和删除 CSI Volume、对 CSI Volume 进行 Attach/Dettach（在 CSI 里，这个操作被叫作 Publish/Unpublish），以及对 CSI Volume 进行 Snapshot 等

- **CSI Node**

  “Mount 阶段”在 CSI Node 里的接口，是由 NodeStageVolume 和 NodePublishVolume 两个接口共同实现的;

  当 kubelet 的 VolumeManagerReconciler 控制循环检查到它需要执行 Mount 操作的时候，会通过 pkg/volume/csi 包，直接调用 CSI Node 服务完成 Volume 的“Mount 阶段”

#### tips

- 在实际使用 CSI 插件的时候，我们会将这三个 External Components 作为 sidecar 容器和 CSI 插件放置在同一个 Pod 中。由于 External Components 对 CSI 插件的调用非常频繁，所以这种 sidecar 的部署方式非常高效。



## RBAC

> Role-Based Access Control

### Operator

> Operator 的工作原理，实际上是利用了 Kubernetes 的自定义 API 资源（CRD），来描述我们想要部署的“有状态应用”；然后在自定义控制器里，根据自定义 API 对象的变化，来完成具体的部署和运维工作

# command

## namespace

```bash
kubectl get namespaces
```



## pod

```shell
kubectl get pod -n {namespace}
```



## log

```shell
kubectl logs -f {podId} -n {namespace}
```



## config info

```
kubectl get pod 容器id --kubeconfig=/path/to/configfile -o yaml > env-vq48.yaml
```

```
kubectl get -o yaml 这样的参数，会将指定的 Pod API 对象以 YAML 的方式展示出来。
```



## exec

- without kubeconfig

```shell
kubectl exec -it  容器id -n 命令空间 -c entity-server-server -- sh
```



## describe

```shell
kubectl describe pod {podName}
```



## cp

```shell
kubectl cp 命令空间/容器id:/path/to/source_file ./path/to/local_file
```

# conclusion

- 过去很多的集群管理项目（比如 Yarn、Mesos，以及 Swarm）所擅长的，都是把一个容器，按照某种规则，放置在某个最佳节点上运行起来。这种功能，我们称为“调度”。

  而 Kubernetes 项目所擅长的，是按照用户的意愿和整个系统的规则，完全自动化地处理好容器之间的各种关系。**这种功能，就是：编排。**



