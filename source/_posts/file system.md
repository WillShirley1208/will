---
title: file system
date: 2024-08-07 13:59:21
tags: learn
categories: fs
---

# 文件系统

![fileSystemType.jpeg](/images/fs/fileSystemType.jpeg)

- Mac 默认可以读 Windows 的 NTFS 格式，但不能写。

- Windows 无法识别 Mac 的 HFS+ 或 APFS 格式。

- Mac 和 Windows 都能正常读写 FAT32 和 ExFAT 格式

- linux
  
  ```
  Linux：存在几十个文件系统类型：ext2，ext3，ext4，xfs，brtfs，zfs（man 5 fs可以取得全部文件系统的介绍）
  
  不同文件系统采用不同的方法来管理磁盘空间，各有优劣；文件系统是具体到分区的，所以格式化针对的是分区，分区格式化是指采用指定的文件系统类型对分区空间进行登记、索引并建立相应的管理表格的过程。
  
  ext2具有极快的速度和极小的CPU占用率，可用于硬盘和移动存储设备
  ext3增加日志功能，可回溯追踪
  ext4日志式文件系统，支持1EB（1024*1024TB），最大单文件16TB，支持连续写入可减少文件碎片。rhel6默认文件系统
  xfs可以管理500T的硬盘。rhel7默认文件系统
  brtfs文件系统针对固态盘做优化，
  ```

- windows
  
  ```
  FAT16：MS—DOS和win95采用的磁盘分区格式，采用16位的文件分配表，只支持2GB的磁盘分区，最大单文件2GB，且磁盘利用率低
  FAT32：（即Vfat）采用32位的文件分配表，支持最大分区128GB，最大文件4GB
  NTFS：支持最大分区2TB，最大文件2TB，安全性和稳定性非常好，不易出现文件碎片。
  ```

---

# concept

## LVM (Logical Volume Manager)

>  Represents the LVM system that manages the disks (physical volumes), creates VGs, and allocates LVs.

​	•	**Disk**: Represents the physical disks, such as /dev/sda or /dev/vdb.

​	•	**VG (Volume Group)**: Aggregates the space of one or more physical disks.

​	•	**LV (Logical Volume)**: A subdivision of the VG space, which can be mounted.

​	•	**Mount Point**: Where the LV is mounted in the filesystem.

<img src="/images/fs/lvm architecture.png">

# FUSE

> Filesystem in Userspace

# command

## delete existed volume

```shell
# step 1:
lsblk
NAME                                                                                                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0                                                                                                    11:0    1 1024M  0 rom  
vda                                                                                                   252:0    0  300G  0 disk 
├─vda1                                                                                                252:1    0    6G  0 part /boot
└─vda2                                                                                                252:2    0  290G  0 part 
  └─rl_172--20--7--230-root                                                                           253:0    0  290G  0 lvm  /
vdb                                                                                                   252:16   0  100G  0 disk 
└─ceph--d79fdfae--bdd5--4ea7--a907--740181f88091-osd--block--37262a3f--354b--4d6a--95db--eb18abd939ce 253:1    0  100G  0 lvm 

# step3:
sudo vgchange -an ceph--d79fdfae--bdd5--4ea7--a907--740181f88091-osd--block--37262a3f--354b--4d6a--95db--eb18abd939ce

# step4
sudo lvchange -an /dev/ceph-d79fdfae-bdd5-4ea7-a907-740181f88091/osd-block-37262a3f-354b-4d6a-95db-eb18abd939ce

# step5
sudo vgremove ceph-d79fdfae-bdd5-4ea7-a907-740181f88091
```

## create lvm 

```shell
# step1 create vg(volume group)
sudo vgcreate s3_vg /dev/vdb

# step2 create lv(logic volume)
sudo lvcreate -L 50G -n minio_lv s3_vg

# step3 format xfs on lvm
sudo mkfs.xfs /dev/s3_vg/minio_lv

# step4 create mount point
sudo mkdir -p /mnt/minio

# step5 mount logic point
sudo mount -o noatime /dev/s3_vg/minio_lv /mnt/minio

# step6 verify
df -h /mnt/minio

Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/s3_vg-minio_lv   50G  389M   50G   1% /mnt/minio
```

## unmount lvm

```shell
# step1 umount directory
sudo umount /mnt/your_mount_point

# step2 Deactivate the Logical Volume
sudo lvchange -an /dev/volume_group/logical_volume

# step3 Remove the Logical Volume
sudo lvremove /dev/volume_group/logical_volume

# (Optional) step4 Remove the Volume Group 
sudo vgremove volume_group

# (Optional) step5 Remove the Physical Volume
sudo pvremove /dev/sdX
```



## other

```shell
# check vgs
sudo vgs
```





reference

[https://www.yinxiang.com/everhub/note/0312ed71-61f5-4c75-9c77-3db0ffdeb613](https://www.yinxiang.com/everhub/note/0312ed71-61f5-4c75-9c77-3db0ffdeb613)