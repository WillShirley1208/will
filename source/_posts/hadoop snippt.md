---
title: hadoop snippet
date: 2019-05-24 12:01:30
tags: snippet
categories: hadoop
---

# Command

## 1. Create a directory in HDFS at given path(s).

```
Usage:
```

```
hdfs dfs -mkdir <paths>
```

```
Example:
```

```
hadoop dfs -mkdir /user/saurzcode/dir1 /user/saurzcode/dir2
```

## 2.  List the contents of a directory.

```
Usage :
```

```
hdfs dfs -ls <args>
```

```
Example:
```

```
hdfs dfs -ls /user/saurzcode
```

## 3. Upload and download a file in HDFS.

**Upload:**

**hdfs fs -put:**

Copy single src file, or multiple src files from local file system to the hdfs data file system

```
Usage:
```

```
hdfs dfs -put <localsrc> ... <HDFS_dest_Path>
```

```
Example:
```

```
hdfs dfs -put /home/saurzcode/Samplefile.txt  /user/saurzcode/dir3/
```

**\*Download:***

**hdfs fs -get:**

Copies/Downloads files to the local file system

```
Usage:
```

```
hdfs dfs -get <hdfs_src> <localdst>
```

```
Example:
```

```
hdfs dfs -get /user/saurzcode/dir3/Samplefile.txt /home/
```

## 4. See contents of a file

Same as unix cat command:

```
Usage:
```

```
hdfs dfs -cat <path[filename]>
```

```
Example:
```

```
hdfs dfs -cat /user/saurzcode/dir1/abc.txt
```

## 5. Copy a file from source to destination

This command allows multiple sources as well in which case the destination must be a directory.

```
Usage:
```

```
hdfs dfs -cp <source> <dest>
```

```
Example:
```

```
hdfs dfs -cp /user/saurzcode/dir1/abc.txt /user/saurzcode/dir2
```

## 6. Copy a file from/To Local file system to HDFS

**copyFromLocal**

```
Usage:
```

```
hdfs dfs -copyFromLocal <localsrc> URI
```

```
Example:
```

```
hdfs dfs -copyFromLocal /home/saurzcode/abc.txt  /user/saurzcode/abc.txt
```

Similar to put command, except that the source is restricted to a local file reference.

**copyToLocal**

```
Usage:
```

```
hdfs dfs -copyToLocal [-ignorecrc] [-crc] URI <localdst>
```

Similar to get command, except that the destination is restricted to a local file reference.

## 7. Move file from source to destination.

Note:- Moving files across filesystem is not permitted.

```
Usage :
```

```
hdfs dfs -mv <src> <dest>
```

```
Example:
```

```
hdfs dfs -mv /user/saurzcode/dir1/abc.txt /user/saurzcode/dir2
```

## 8. Remove a file or directory in HDFS.

Remove files specified as argument. Deletes directory only when it is empty

```
Usage :
```

```
hdfs dfs -rm <arg>
```

```
Example:
```

```
hdfs dfs -rm /user/saurzcode/dir1/abc.txt
```

**\*Recursive version of delete.***

```
Usage :
```

```
hdfs dfs -rmr <arg>
```

```
Example:
```

```
hdfs dfs -rmr /user/saurzcode/
```

## 9. Display last few lines of a file.

Similar to tail command in Unix.

```
Usage :
```

```
hdfs dfs -tail <path[filename]>
```

```
Example:
```

```
hdfs dfs -tail /user/saurzcode/dir1/abc.txt
```

## 10. Display the aggregate length of a file.

```
Usage :
```

```
hdfs dfs -du <path>
```

```
Example:
hdfs dfs -du /user/saurzcode/dir1/abc.txt
```

Please comment which of these commands you found most useful while dealing with Hadoop /HDFS.



# Solution

## Hadoop Operation category READ is not supported in state standby

- 报错原因
  hadoop集群中的两个NameNode均为standby状态。

  ```shell
  hdfs haadmin -getServiceState nn1
  hdfs haadmin -getServiceState nn2
  ```

​		如果两个NameNode均为standby状态，那么很有可能是DFSZKFailoverController出现了问题。

- 报错解决

  - 手动改变其中一个NameNode的状态

    ```shell
    hdfs haadmin -transitionToActive -forcemanual nn1
    ```

  - 如果手动改变状态不能解决问题，那就重启dfs

    ```shell
    stop-dfs.sh
    start-dfs.sh
    ```

- 再检查NameNode状态

  此时正常状态是nn1为active，nn2为standby