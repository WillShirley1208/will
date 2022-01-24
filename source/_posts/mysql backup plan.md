# mysql数据库备份

## 方案一：定期备份数据库数据文件

### 一、编写shell脚本

脚本文件**backup_mysql.sh**信息如下：

```shell
#用户名
username=root
#密码
password=填写密码
#将要备份的数据库
database_name=填写需要备份的数据库

#保存备份文件最多个数
count=30
#备份保存路径
backup_path=/data/mysql_backup
#日期
date_time=`date +%Y-%m-%d-%H-%M`

#如果文件夹不存在则创建
if [ ! -d $backup_path ]; 
then     
    mkdir -p $backup_path; 
fi
#开始备份
mysqldump -u $username -p$password $database_name > $backup_path/$database_name-$date_time.sql
#开始压缩
cd $backup_path
tar -zcvf $database_name-$date_time.tar.gz $database_name-$date_time.sql
#删除源文件
rm -rf $backup_path/$database_name-$date_time.sql
#更新备份日志
echo "create $backup_path/$database_name-$date_time.tar.gz" >> $backup_path/dump.log

#找出需要删除的备份
delfile=`ls -l -crt $backup_path/*.tar.gz | awk '{print $9 }' | head -1`

#判断现在的备份数量是否大于阈值
number=`ls -l -crt  $backup_path/*.tar.gz | awk '{print $9 }' | wc -l`

if [ $number -gt $count ]
then
  #删除最早生成的备份，只保留count数量的备份
  rm $delfile
  #更新删除文件日志
  echo "delete $delfile" >> $backup_path/dump.log
fi
```

该脚本实现的功能：备份指定数据库的数据信息到指定目录，并只保存指定数量的最新文件。

注意：脚本中需要补全脚本中的**password**和**database_name**信息，可修改备份保存路径**backup_path**，以及最多保存的备份文件数量**count**。

编写完脚本信息之后，需要给脚本赋予可执行权限 `chmod +x backup_mysql.sh`

### 二、设定定时任务crontab

运行crontab -e命令，打开一个可编辑的文本，输入`0 1 * * * /path/to/backup_mysql.sh`  保本并退出即添加完成。

注意：其中`0 1 * * *`，表示每天凌晨1点进行备份操作，可自行修改1的值（范围0～23）

其中路径信息`/path/to/backup_mysql.sh`需要修改为实际的脚本路径。
