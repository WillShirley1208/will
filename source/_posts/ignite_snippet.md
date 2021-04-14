---
title: ignite snippet
date: 2019-11-01 12:01:30
tags: snippet
categories: ignite
---

## ignite-2.7.0

-  编译ignite-core模块的时候需要使用jdk8，因为会报jdk.internal.misc.SharedSecrets找不到的错误

  原因：估计是在GridUnsafe.java中的miscPackage方法以及javaNioAccessObject的`Class<?> cls = Class.forName(pkgName + ".misc.SharedSecrets");`出现的问题，jdk11中SharedSecrets出现的位置是`jdk.internal.access.SharedSecrets`



### 问题

#### 1. BinaryObjectException: Conflicting enum values

- 原因

  ```
  存入ignite的数据格式
  key: String , value: Map<Enum, BigDecimal>
  Enum类型包含
  {
    A,
    B,
    C
  }
  
  在之后由于业务变更，需要新增新的enum项目，并添加D在A与B之间
  {
      A,
      D,
      B,
      C
  }
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



