---
title: minio snippet
date: 2024-09-29 10:25:00
tags: snippet
categories: minio
---

# concept

## Erasure Coding

**Erasure Coding** splits data into data and parity shards. For example, MinIO might use a **4:2 erasure code** (should use 6  disks), meaning each object is split into 4 data shards and 2 parity shards.

> In a 4:2 erasure coding setup:
>
> 4 data shards hold the actual object data.
>
> 2 parity shards are created for redundancy.

In this configuration, every object is broken into smaller pieces, with redundancy pieces (parity shards) stored to allow recovery if a disk or node fails.

## server

```shell
  # service file
  /usr/lib/systemd/system/minio.service
  
  # default config file
  /etc/default/minio
   
  # reload config after modify default file
  sudo systemctl stop minio
  sudo systemctl daemon-reload
  
  # 如果更改MINIO_VOLUMES信息，并且需要用到之前的一些盘符，需要清理干净相关目录的文件（包括隐藏文件 .minio.sys）
  需要目录里清理遗留的所有文件 （rm -rf ./* 命令不会清理隐藏文件）
```

# client

## install

  ```shell
  curl https://dl.min.io/client/mc/release/linux-amd64/mc \
    --create-dirs \
    -o $HOME/minio-binaries/mc
  
  chmod +x $HOME/minio-binaries/mc
  
  cp $HOME/minio-binaries/mc /usr/bin/
  
  mc alias set myminio http://minio1.dev.net:19000 {ak} {sk}
  ```

## command

- development

```shell
# add development
mc alias set myminio {endpoint_address} {ak} {sk} 
# e.g. mc alias set myminio http://minio1.dev.net:19000 1111111 22222222

# ls alias
mc alias list

# overview info
mc admin info myminio

# check config 
mc admin config get myminio
```
- bucket
```shell
# create bucket
mc mb <ALIAS>/<BUCKET>

# list bucket
mc ls <ALIAS>

# check bucket
mc ls <ALIAS>/<BUCKET>

# check object
mc cat <ALIAS>/<BUCKET>/<OBJECT>

# delete object
mc rm <ALIAS>/<BUCKET>/<OBJECT>

# delete all data
mc rm --recursive --force <ALIAS>/<BUCKET>

# force delete bucket
mc rb --force <ALIAS>/<BUCKET>
```

- erasure code

```shell
# set erasure code
mc admin config set myminio storage_class standard=EC:0
# should restart minio.service, and 'systemctl status minio' will print 'WARN: The standard parity is set to 0. This can lead to data loss'

# check storage_class 
mc admin config get myminio storage_class
```

