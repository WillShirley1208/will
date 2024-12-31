---
title: lvm senario
date: 2024-12-06 10:43:30
tags: lvm
categories: fs
---

# overview

<img src="/images/fs/lvm_overview-1.svg">

# create LVs 

>  If you want to create **three Logical Volumes (LVs)** of sizes 1TB, 1TB, and 5TB on a single Volume Group (VG) for each disk, here’s how you can do it step by step.

Assume:

​	•	**Disks**: /dev/nvme0n1, /dev/nvme1n1, /dev/nvme2n1

​	•	**Volume Groups**: vg_nvme0, vg_nvme1, vg_nvme2

​	•	**Logical Volumes (LVs)**: lv1, lv2, lv3 for each VG

​	•	**LV Sizes**: 1TB, 1TB, and 5TB

## 1. Create Physical Volumes (PVs)

```shell
# Initialize each disk as a PV:
sudo pvcreate /dev/nvme0n1
sudo pvcreate /dev/nvme1n1
sudo pvcreate /dev/nvme2n1
```

## 2. Create Volume Groups (VGs)

```shell
# Create a VG for each disk:
sudo vgcreate vg_nvme0 /dev/nvme0n1
sudo vgcreate vg_nvme1 /dev/nvme1n1
sudo vgcreate vg_nvme2 /dev/nvme2n1
```

## 3. Create Logical Volumes (LVs)

```shell
# Allocate 1TB, 1TB, and 5TB LVs from each VG.

# For vg_nvme0:
sudo lvcreate -L 1T -n lv1 vg_nvme0
sudo lvcreate -L 1T -n lv2 vg_nvme0
sudo lvcreate -L 5T -n lv3 vg_nvme0

# For vg_nvme1:
sudo lvcreate -L 1T -n lv1 vg_nvme1
sudo lvcreate -L 1T -n lv2 vg_nvme1
sudo lvcreate -L 5T -n lv3 vg_nvme1

# For vg_nvme2:
sudo lvcreate -L 1T -n lv1 vg_nvme2
sudo lvcreate -L 1T -n lv2 vg_nvme2
sudo lvcreate -L 5T -n lv3 vg_nvme2
```

## 4. Verify the Setup

```shell
# Check the LVs and their sizes:
sudo lvs
```

You should see something like:

```shell
LV   VG        Attr       LSize

lv1  vg_nvme0  -wi-a----- 1.00t                           
lv2  vg_nvme0  -wi-a----- 1.00t                           
lv3  vg_nvme0  -wi-a----- 5.00t                           
lv1  vg_nvme1  -wi-a----- 1.00t                           
lv2  vg_nvme1  -wi-a----- 1.00t                           
lv3  vg_nvme1  -wi-a----- 5.00t                           
lv1  vg_nvme2  -wi-a----- 1.00t                           
lv2  vg_nvme2  -wi-a----- 1.00t                           
lv3  vg_nvme2  -wi-a----- 5.00t
```

## 5. Format the LVs

```shell
# Use xfs or another filesystem to format the LVs:
sudo mkfs.xfs /dev/vg_nvme0/lv1
sudo mkfs.xfs /dev/vg_nvme0/lv2
sudo mkfs.xfs /dev/vg_nvme0/lv3

sudo mkfs.xfs /dev/vg_nvme1/lv1
sudo mkfs.xfs /dev/vg_nvme1/lv2
sudo mkfs.xfs /dev/vg_nvme1/lv3

sudo mkfs.xfs /dev/vg_nvme2/lv1
sudo mkfs.xfs /dev/vg_nvme2/lv2
sudo mkfs.xfs /dev/vg_nvme2/lv3
```

## 6. Mount the LVs

```shell
# Create directories for each LV and mount them:
sudo mkdir -p /mnt/nvme0/lv1 /mnt/nvme0/lv2 /mnt/nvme0/lv3
sudo mkdir -p /mnt/nvme1/lv1 /mnt/nvme1/lv2 /mnt/nvme1/lv3
sudo mkdir -p /mnt/nvme2/lv1 /mnt/nvme2/lv2 /mnt/nvme2/lv3

sudo mount /dev/vg_nvme0/lv1 /mnt/nvme0/lv1
sudo mount /dev/vg_nvme0/lv2 /mnt/nvme0/lv2
sudo mount /dev/vg_nvme0/lv3 /mnt/nvme0/lv3

sudo mount /dev/vg_nvme1/lv1 /mnt/nvme1/lv1
sudo mount /dev/vg_nvme1/lv2 /mnt/nvme1/lv2
sudo mount /dev/vg_nvme1/lv3 /mnt/nvme1/lv3

sudo mount /dev/vg_nvme2/lv1 /mnt/nvme2/lv1
sudo mount /dev/vg_nvme2/lv2 /mnt/nvme2/lv2
sudo mount /dev/vg_nvme2/lv3 /mnt/nvme2/lv3
```

## 7. Persist the Mounts

```shell
# Add the mounts to /etc/fstab for automatic remounting on boot:
echo '/dev/vg_nvme0/lv1 /mnt/nvme0/lv1 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme0/lv2 /mnt/nvme0/lv2 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme0/lv3 /mnt/nvme0/lv3 xfs defaults 0 0' | sudo tee -a /etc/fstab

echo '/dev/vg_nvme1/lv1 /mnt/nvme1/lv1 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme1/lv2 /mnt/nvme1/lv2 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme1/lv3 /mnt/nvme1/lv3 xfs defaults 0 0' | sudo tee -a /etc/fstab

echo '/dev/vg_nvme2/lv1 /mnt/nvme2/lv1 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme2/lv2 /mnt/nvme2/lv2 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme2/lv3 /mnt/nvme2/lv3 xfs defaults 0 0' | sudo tee -a /etc/fstab
```

```shell
# **Final Directory Layout**
# **Disk**	**VG Name**	**LV Name**	**Size**	**Mount Point**
/dev/nvme0n1	vg_nvme0	lv1	1T	/mnt/nvme0/lv1
		lv2	1T	/mnt/nvme0/lv2
		lv3	5T	/mnt/nvme0/lv3

/dev/nvme1n1	vg_nvme1	lv1	1T	/mnt/nvme1/lv1
		lv2	1T	/mnt/nvme1/lv2
		lv3	5T	/mnt/nvme1/lv3

/dev/nvme2n1	vg_nvme2	lv1	1T	/mnt/nvme2/lv1
		lv2	1T	/mnt/nvme2/lv2
		lv3	5T	/mnt/nvme2/lv3
```