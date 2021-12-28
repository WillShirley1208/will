---
title: 挖矿程序
date: 2019-07-31 10:00:30
tags: snippet
categories: linux
---

```
config.json （挖矿配置）、
sysupdate （XMR 挖矿软件）、
update.sh （本脚本）、
networkservice(scanner扫描并入侵其他的主机)、
sysguard(watchdog 用于监控并保证病毒的正常运行以及更新) 并保证他们以 root 权限运行。
```

1. 查看病毒的 PID 号

2. 获取绝对路径

   ```shell
   ls -l /proc/{pid 号}/exe
   ```

3. 删除/tmp文件夹的所有文件

4. 检查下定时任务

   ```sh
   crontab -l 或者 cat /var/spool/cron/will
   ```

   上面的will为当前用户名

   查看定时任务的日志

   ```sh
   more /var/log/cron log
   ```

4. 删除定时任务

   ```sh
   rm /var/spool/cron/root 或者 crontab -r 删除定时任务
   ```

5. kill 命令将相关进程干掉

   ```sh
   sysupdate
   networkservice
   sysguard
   ```

   

