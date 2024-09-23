---
title: kubectl command
date: 2024-09-15 15:45:47
tags: command
categories: kubectl
---

# common

### version

```shell
kubectl version
kubectl api-versions
```

### health

```shell
# check kubernetes inner ip 
kubectl get svc kubernetes -n default

# check api server
kubectl get componentstatuses

# check crd
kubectl get crd | grep cert-manager

# check all pods
kubectl get pods -A
```

### label

```shell
# check node label
kubectl get nodes --show-labels
```



### namespace

```bash
kubectl get namespaces
```

### check pod

```shell
kubectl get pod -n {namespace}
```

### delete pod

```shell
kubectl delete pod {pod_name} --grace-period=0 --force -n {namespace}
```



### expose yaml

```shell
kubectl get pod <pod-name> -n <namespace> -o yaml > pod-config.yaml
```

###  daemonsets

```shell
kubectl get daemonsets --all-namespaces
```



### log

​	•	-c <container-name>: Specify which container to retrieve logs from.

​	•	-f: Stream the logs in real-time.

​	•	--previous: Show logs from the last terminated container.

​	•	--since=<duration>: Return logs for the last period (e.g., 1h, 30m).

​	•	--tail=<lines>: Limit the number of log lines returned.

​	•	--all-containers=true: Get logs from all containers in the pod.

```shell
# pod
kubectl logs -f {podId} -n {namespace}

# pod's container
kubectl logs <pod-name> -c <container-name> -n <namespace>
```

### config info

```
kubectl get pod 容器id --kubeconfig=/path/to/configfile -o yaml > env-vq48.yaml
```

```
kubectl get -o yaml 这样的参数，会将指定的 Pod API 对象以 YAML 的方式展示出来。
```

### exec

without kubeconfig

```shell
# kubectl exec -it {pod_name} -- /bin/bash
kubectl exec -it {pod_id} -n {namespace} -c {container_id} -- sh
```

### describe

```shell
kubectl describe pod {podName}
```

### cp

```shell
kubectl cp 命令空间/容器id:/path/to/source_file ./path/to/local_file
```

## storage

### CSIDriver

```shell
kubectl get csidrivers
```

### storageclass

```shell
kubectl get storageclass
```

### pvc

```shell
kubectl get pvc
```



