---
title: Kubernetes controller
date: 2024-12-15 22:20:05
tags: controller runtime
categories: Kubernetes
---

> From [Diving into controller-runtime | Manager](https://buraksekili.github.io/articles/controller-runtime-1/)

[`controller-runtime`](https://github.com/kubernetes-sigs/controller-runtime) package has become a fundamental tool for most Kubernetes controllers, simplifying the creation of controllers to manage resources within a Kubernetes environment efficiently. Users tend to prefer it over [`client-go`](https://github.com/kubernetes/client-go).

In Kubernetes, controllers observe resources, such as Deployments, in a control loop to ensure the cluster resources conform to the desired state specified in the resource specification (e.g., YAML files).

On the other hand, according to Redhat, a Kubernetes Operator is an application-specific controller [2](https://buraksekili.github.io/articles/controller-runtime-1/#fn:2). For instance, the Prometheus Operator manages the lifecycle of a Prometheus instance in the cluster, including managing configurations and updating Kubernetes resources, such as ConfigMaps.

# Architecture

controllers follow a special architecture to

- observe the resources,
- inform any events (updating, deleting, adding) done on the resources,
- keep a local cache to decrease the load on API Server,
- keep a work queue to pick up events,
- run workers to perform reconciliation on resources picked up from work queue.

<img src="/images/k8s/controller-runtime-1-client-go-controller-interaction.jpeg" style="zoom: 80%">

## informer

Informers leverage certain components like **Reflector**, **Queue** and **Indexer**, as shown in the above diagram.

Informers watch Kubernetes API server to detect changes in resources that we want to. It keeps a local cache - in-memory cache implementing [Store](https://pkg.go.dev/k8s.io/client-go/tools/cache#Store) interface - including the objects observed through Kubernetes API. Then controllers and operators use this cache for all getter requests - GET and LIST - to prevent load on Kubernetes API server. Moreover, Informers invoke controllers by sending objects to the controllers (registering Event Handlers).

### Reflector

> Reflector watches a specified resource and causes all changes to be reflected in the given store.

The store is actually a cache - with two options; simple one and FIFO. Reflector pushes objects to Delta Fifo queue.

By monitoring the server (Kubernetes API Server), the Reflector maintains a local cache of the resources. Upon any event occurring on the watched resource, implying a new operation on the Kubernetes resource, the Reflector updates the cache (Delta FIFO queue, as illustrated in the diagram). Subsequently, the Informer reads objects from this Delta FIFO queue, indexes them for future retrievals, and dispatches the object to the controller.

### Indexer

Indexer saves objects into thread-safe Store by indexing the objects. This approach facilitates efficient querying of objects from the cache.

Custom indexers, based on specific needs, can be created. For example, a custom indexer can be generated to retrieve all objects based on certain fields, such as Annotations.

More details about how Kubernetes indexing works, check [Kubernetes Client-Side Indexing](https://buraksekili.github.io/articles/client-k8s-indexing).





---

reference

- [Kubernetes Controller 机制详解](https://www.zhaohuabing.com/post/2023-03-09-how-to-create-a-k8s-controller/)

- [Controller Runtime 的四种使用姿势](https://cloud.tencent.com/developer/article/1989055)