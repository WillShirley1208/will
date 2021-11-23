---
title: bigdata snippet
date: 2019-05-05 22:52:30
tags: snippet
categories: bigdata
---

## BIG DATA: What is, Types, Characteristics & Example

- Big Data is defined as data that is huge in size. Bigdata is a term used to describe a collection of data that is huge in size and yet growing exponentially with time.
- Examples of Big Data generation includes stock exchanges, social media sites, jet engines, etc.
- Big Data could be 1) Structured, 2) Unstructured, 3) Semi-structured
- Volume, Variety, Velocity, and Variability are few Characteristics of Bigdata
- Improved customer service, better operational efficiency, Better Decision Making are few advantages of Bigdata

## Hadoop EcoSystem and Components

![](./img/061114_0803_LearnHadoop4.png)

### Hadoop Architecture

![](./img/hadoop-architecture.png)

#### HDFS Architecture

HDFS cluster primarily consists of a **NameNode** that manages the file system **Metadata** and a **DataNodes** that stores the **actual data**.

- Read Operation In HDFS

  ![](./img/061114_0923_LearnHDFSAB1.png)

- Write Operation In HDFS

  ![](./img/061114_0923_LearnHDFSAB2.png)

#### MapReduce

The whole process goes through four phases of execution namely, splitting, mapping, shuffling, and reducing.

![](./img/061114_0930_Introductio1.png)

1. **Jobtracker**: Acts like a **master** (responsible for complete execution of submitted job)
2. **Multiple Task Trackers**: Acts like **slaves,** each of them performing the job

![](./img/061114_0930_Introductio2.png)