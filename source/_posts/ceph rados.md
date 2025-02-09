---
title: ceph rados
date: 2025-02-08 11:40:20
tags: storage
categories: ceph
---

The [Ceph Storage Cluster](https://docs.ceph.com/en/reef/glossary/#term-Ceph-Storage-Cluster) provides the basic storage service that allows [Ceph](https://docs.ceph.com/en/reef/glossary/#term-Ceph) to uniquely deliver **object, block, and file storage** in one unified system. However, you are not limited to using the RESTful, block, or POSIX interfaces. Based upon RADOS, the `librados` API enables you to create your own interface to the Ceph Storage Cluster.

# command

```shell
# list objects by specify pool
rados -p <pool-name> ls

# read object
rados -p <pool-name> get <object-name> output.txt
cat output.txt

# Check xattr value
rados -p <pool-name> getxattr <object-name> <xattr-key>
```

