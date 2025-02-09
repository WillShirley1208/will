---
title: ceph deploy
date: 2025-02-07 17:40:41
tags: deploy
categories: ceph
---

# deploy

## install cephadm

```shell
dnf search release-ceph
dnf install --assumeyes centos-release-ceph-reef
dnf install --assumeyes cephadm
```

- enable ceph cli

  ```shell
  cephadm add-repo --release reef
  cephadm install ceph-common
  ```

## booststrap

```shell
cephadm bootstrap --mon-ip 172.20.7.232
```

- log
```shell
Ceph Dashboard is now available at:
        URL: https://dingo7232.com:8443/
        User: admin
    Password: <password>
Enabling client.admin keyring and conf on hosts with "admin" label
Saving cluster configuration to /var/lib/ceph/6a65c746-e532-11ef-8ac2-fa7c097efb00/config directory
Enabling autotune for osd_memory_target
You can access the Ceph CLI as following in case of multi-cluster or non-default config:

        sudo /sbin/cephadm shell --fsid 6a65c746-e532-11ef-8ac2-fa7c097efb00 -c /etc/ceph/ceph.conf -k /etc/ceph/ceph.client.admin.keyring

Or, if you are only running a single cluster on this host:

        sudo /sbin/cephadm shell

Please consider enabling telemetry to help improve Ceph:

        ceph telemetry on

For more information see:

        https://docs.ceph.com/en/latest/mgr/telemetry/

Bootstrap complete.

```


## add hosts

- Install the cluster’s public SSH key in the new host’s root user’s authorized_keys file:
  ```shell
  ssh-copy-id -f -i /etc/ceph/ceph.pub root@dingo7233
  ssh-copy-id -f -i /etc/ceph/ceph.pub root@dingo7234
  ```

- Tell Ceph that the new node is part of the cluster
  ```shell
  # ceph orch host add *<newhost>* [*<ip>*] [*<label1> ...*]
  ceph orch host add dingo7233 172.20.7.233
  ceph orch host add dingo7234 172.20.7.234
  or
  ceph orch host add dingo7233 172.20.7.233 --labels _admin
  ceph orch host add dingo7234 172.20.7.234 --labels _admin
  ```

- add label (optional)
  ```shell
  ceph orch host label add dingo7233 _admin
  ceph orch host label add dingo7234 _admin
  ```

- list hosts

  ```shell
  ceph orch host ls --detail
  ```

## add storage

- check available devices
  ```shell
  ceph orch device ls
  ```

- apply osd
  ```shell
  ceph orch apply osd --all-available-devices
  ```

# check
```shell
# ceph status
cluster:
    id:     6a65c746-e532-11ef-8ac2-fa7c097efb00
    health: HEALTH_WARN
            1 mgr modules have recently crashed

  services:
    mon: 3 daemons, quorum dingo7232,dingo7234,dingo7233 (age 16m)
    mgr: dingo7232.znvodw(active, since 24m), standbys: dingo7234.yenqrt
    osd: 3 osds: 3 up (since 15m), 3 in (since 15m)

  data:
    pools:   1 pools, 1 pgs
    objects: 2 objects, 449 KiB
    usage:   81 MiB used, 600 GiB / 600 GiB avail
    pgs:     1 active+clean
```


# thouble shooting
## redeploy cluster
To remove an existing Ceph cluster deployed using `cephadm` and redeploy a new one, follow these steps:

- Step 1: Stop All Ceph Services

First, stop all Ceph services on each host in the cluster.

```bash
sudo systemctl stop ceph.target
```

- Step 2: Remove Ceph Configuration and Data

Remove the Ceph configuration and data directories.

```bash
sudo rm -rf /etc/ceph
sudo rm -rf /var/lib/ceph
sudo rm -rf /var/log/ceph
```

- Step 3: deploy as below words

- Step 4: Verify Cluster Health

  ```shell
  ceph -s
  ```

If you encounter any issues during the redeployment, check the logs:

```bash
sudo journalctl -u ceph-* -f
```

Or check the Ceph logs directly:

```bash
sudo less /var/log/ceph/ceph.log
```
