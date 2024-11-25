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

- minio.service

  ```yaml
  [Unit]
  Description=MinIO
  Documentation=https://docs.min.io
  Wants=network-online.target
  After=network-online.target
  AssertFileIsExecutable=/usr/local/bin/minio
  
  [Service]
  Type=notify
  
  WorkingDirectory=/usr/local
  
  User=dingofs
  Group=dingofs
  ProtectProc=invisible
  
  EnvironmentFile=-/etc/default/minio
  ExecStart=/usr/local/bin/minio server $MINIO_OPTS $MINIO_VOLUMES
  
  # Let systemd restart this service always
  Restart=always
  
  # Specifies the maximum file descriptor number that can be opened by this process
  LimitNOFILE=1048576
  
  # Turn-off memory accounting by systemd, which is buggy.
  MemoryAccounting=no
  
  # Specifies the maximum number of threads this process can create
  TasksMax=infinity
  
  # Disable timeout logic and wait until process is stopped
  TimeoutSec=infinity
  
  SendSIGKILL=no
  
  [Install]
  WantedBy=multi-user.target
  ```

- /etc/default/minio

  ```yaml
  MINIO_VOLUMES="http://minio{1...3}.dev.net:19000/mnt/disk2/minio"
  #MINIO_VOLUMES="http://minio{1...3}.dev.net:19000/mnt/disk{0...1}/minio"
  #MINIO_VOLUMES="http://minio1.dev.net:19000/mnt/disk1/minio http://minio2.dev.net:19000/mnt/disk1/minio http://minio3.dev.net:19000/mnt/disk1/minio"
  #MINIO_VOLUMES="http://minio2.dev.net:19000/mnt/minio http://minio3.dev.net:19000/mnt/minio"
  
  # Set all MinIO server options
  #
  # The following explicitly sets the MinIO Console listen address to
  # port 9001 on all network interfaces. The default behavior is dynamic
  # port selection.
  
  #MINIO_OPTS="--console-address :9001"
  MINIO_OPTS=' --console-address=":19001" --address=":19000" '
  
  # Set the root username. This user has unrestricted permissions to
  # perform S3 and administrative API operations on any resource in the
  # deployment.
  #
  # Defer to your organizations requirements for superadmin user name.
  
  MINIO_ROOT_USER=minioadmin
  
  # Set the root password
  #
  # Use a long, random, unique string that meets your organizations
  # requirements for passwords.
  
  MINIO_ROOT_PASSWORD=minioadmin
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

- log

```shell
mc admin logs myminio
```



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

# count total object/file num
mc ls --summarize --recursive gateway-minio

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

