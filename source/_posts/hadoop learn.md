---
title: javarevisited
date: 2019-05-24 12:01:30
tags: blog
categories: java
---

![](./img/Hadoop-Multi-node-Cluster.png)

![](./img/hdfsarchitecture.gif)

### NameNode

- The **NameNode** executes file system namespace operations like opening, closing, and renaming files and directories.It also determines the mapping of blocks to DataNodes.

### DataNodes

- The **DataNodes** are responsible for serving read and write requests from the file system’s clients. The DataNodes also perform block creation, deletion, and replication upon instruction from the NameNode.
- The DataNode stores HDFS data in files in its local file system.The DataNode has no knowledge about HDFS files. It stores each block of HDFS data in a separate file in its local file system.

### File

- a file is split into one or more blocks and these blocks are stored in a set of DataNodes. 

- all blocks in a file except the last block are the same size.

- Files in HDFS are write-once and have strictly one writer at any time.

### The Persistence of File System Metadata

- The NameNode uses a transaction log called the **EditLog** to persistently record every change that occurs to file system metadata.
- The entire file system namespace, including the mapping of blocks to files and file system properties, is stored in a file called the **FsImage**. The FsImage is stored as a file in the NameNode’s local file system too.

---

**Blockreport**: DataNode scans through its local file system, generates a list of all HDFS data blocks that correspond to each of these local files and sends this report to the NameNode

### 源码

2.7.3源码

- 编译问题

```
问题：Failed to parse plugin descriptor for org.apache.hadoop:hadoop-maven-plugins

解决：cd到hadoop-maven-plugins，执行mvn clean 然后 install 一下，再执行编译通过
```
