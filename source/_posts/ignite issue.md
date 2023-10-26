---
title: ignite issue
date: 2021-12-09 15:22:02
tags: issue
categories: ignite
---

### BinaryObjectException: Conflicting enum values

- 原因
  
  ```
  存入ignite的数据格式
  key: String , value: Map<Enum, BigDecimal>
  Enum类型包含
  {A,B,C}
  
  在之后由于业务变更，需要新增新的enum项目，并添加D在A与B之间
  {A,D,B,C}
  ```

- 分析
  
  ```
  由于在数据存入ignite之后，ignite会保存数据相关的schema信息,此时在enum项目之间修改item，会打乱之前的index
  ```

- 解决 
  
  ```
  方法一：
  更改enum类的名称，不再使用原有的schema信息
  方法二：
  enum类新增项目时，需要在最后面添加，避免打乱已有的schema索引
  方法三（未验证）：
  删除 $IGNITE_HOME/work/binary_meta/Nodex里面的文件
  ```

- 官方说明
  
  > - You cannot change the types of existing fields.
  > - You cannot change the order of enum values or add new constants at the beginning or in the middle of the list of enum’s values. You can add new constants to the end of the list though.

- **处理conflict enum values, 需要清除数据**

> 需要清理 $IGNITE_HOME/work/db目录下的 binary_meta、marshaller
> 
> 需要验证是否清理 storagePath、walPath、walArchivePath

### gc

![](/images/ignite/gc_collect.png)

> 3s进行gc  (110060-52672)/1024=56.04G

![](/images/ignite/gc_2.png)

> 17s进行gc (109952-52658)/1024=55.95G

### IgniteCacheException

```verilog
ERROR org.apache.ignite.spi.communication.tcp.TcpCommunicationSpi  [] - Failed to send message to remote node [node=ZookeeperClusterNode [id=1c8a032d-042e-4386-9ce8-2605c0699304, addrs=[17.9.11.11], order=1, loc=false, client=false], msg=GridIoMessage [plc=2, topic=TOPIC_CACHE, topicOrd=8, ordered=false, timeout=0, skipOnTimeout=false, msg=GridNearLockRequest [topVer=AffinityTopologyVersion [topVer=358, minorTopVer=0], miniId=1, dhtVers=GridCacheVersion[] [null], subjId=a5dbdc1d-e76e-49c2-85d7-ed7f1c7db7bd, taskNameHash=0, createTtl=-1, accessTtl=-1, flags=3, txLbl=null, filter=null, super=GridDistributedLockRequest [nodeId=a5dbdc1d-e76e-49c2-85d7-ed7f1c7db7bd, nearXidVer=GridCacheVersion [topVer=245500806, order=1638786801426, nodeOrder=336], threadId=11960694, futId=96c1bf42d71-90702925-3ef9-4c70-b7a7-4be2fb6d75ba, timeout=0, isInTx=true, isInvalidate=false, isRead=true, isolation=REPEATABLE_READ, retVals=[true], txSize=0, flags=0, keysCnt=1, super=GridDistributedBaseMessage [ver=GridCacheVersion [topVer=245500806, order=1638786801426, nodeOrder=336], committedVers=null, rolledbackVers=null, cnt=1, super=GridCacheIdMessage [cacheId=-182240380, super=GridCacheMessage [msgId=1360862, depInfo=null, lastAffChangedTopVer=AffinityTopologyVersion [topVer=336, minorTopVer=0], err=null, skipPrepare=false]]]]]]]

org.apache.ignite.IgniteCheckedException: Failed to connect to node due to unrecoverable exception (is node still alive?). Make sure that each ComputeTask and cache Transaction has a timeout set in order to prevent parties from waiting forever in case of network issues [nodeId=d0a258e5-ec1b-4f79-89ad-80c27708f895, addrs=[x/x.x.x.x:47100], err= class org.apache.ignite.IgniteCheckedException: Remote node does not observe current node in topology : d0a258e5-ec1b-4f79-89ad-80c27708f895]

Caused by: org.apache.ignite.IgniteCheckedException: Remote node does not observe current node in topology : d0a258e5-ec1b-4f79-89ad-80c27708f895
```

- gc的策略
- ignite client的异常捕获
- 