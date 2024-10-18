---
title: ubuntu snippet
date: 2021-10-08 17:07:33
tags: snippet
categories: ubuntu
---



## 磁盘挂载

1. Get the UUID of the partition

   `sudo blkid`

2. Add partition to fstab

   ```shell
   # /data was on /dev/sda3
   UUID=0009091A00027E42 /data            ntfs   defaults,iocharset=utf8,fmask=111,dmask=002        0       2
   ```

## deepin wine



## 百度网盘id

- 一直停留在登录界面

  ```
  删除　～/baidunetdisk文件夹，再次启动即可
  ```

## 光驱相关

CDROM, DVD 这些设备现在来说都是属于古董了，但因为有些第三方软件是以CDROM的形式提供的，所以有时候还会遇到如何挂载的问题?

在Ubuntu Server 18.04中，要挂载 CDROM(DVD)可以遵循以下步骤:

#### 挂载 CDROM(DVD)

1. 确认设备文件名, 进入 dev 目录， 执行：

```
cd /dev
ls -lt | less
```

系统显示一个设备列表，可以看到其中有两个特别的，分别是:

```
cdrom -> sr0
dvd -> sr0
```

说明系统已经将 cdrom, dvd 命名为 sr0 的别名，其实是同一个设备。

1. 执行 mount 命令挂载cdrom, 因为 ubuntu server 18.04中，已经在根目录下建了一个名为 cdrom 的目录，我们就使用这个目录来挂载，执行:

```
sudo mount /dev/cdrom /cdrom
```

#### 卸载 CDROM (DVD)

要想卸载 CDROM, 可以简单的执行:

```
sudo umount /cdrom
```

如果是想推出当前的光盘，则可以执行:

```
sudo eject /cdrom
```

## postgre

在本机安装，非docker，user: postgres 	pw:postgres 

```shell
sudo -u postgres psql
```

## 更改 Ubuntu 18.04 登陆背景

1. `sudo vi /etc/alternatives/gdm3.css`

2. 修改 CSS 文件内容

   ```css
   #lockDialogGroup {
   background: url(/*自己喜欢的图片的路径。注意，不需要resource://开头*/);
   background-repeat: no-repeat;
   background-size: cover;
   background-position: center;
   }
   ```

## 配合electron-ssr进行终端外网访问

在`/etc/profile`中添加配置

```
export http_proxy="http://127.0.0.1:12333"
export https_proxy="http://127.0.0.1:12333"
```

然后`source /etc/profile`即可

## 升级到2204版本中文输入法只在终端可用解决

> 参考：https://zhuanlan.zhihu.com/p/508797663

- /etc/profile 添加

```
export XMODIFIERS=@im=fcitx
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
```

- ~/.pam_environment 添加以下几行：

```
GTK_IM_MODULE DEFAULT=fcitx
QT_IM_MODULE DEFAULT=fcitx
XMODIFIERS DEFAULT=@im=fcitx
SDL_IM_MODULE DEFAULT=fcitx
```

