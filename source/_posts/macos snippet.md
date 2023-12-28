---
title: macos snippet
date: 2021-12-20 11:25:00
tags: snippet
categories: macos
---

## Install .pkg

a.

```shell
sudo installer -pkg /path/to/package.pkg -target /
```

> will install the package in /Applications.

is all that's needed. Here `/` is the mount point of `Macintosh HD` volume. `-target` accepts path like `"/Volumes/Macintosh HD"`, or `/dev/disk0` also.

b.

```shell
installer -pkg myapp.pkg -target CurrentUserHomeDirectory
```

> will install the package in ~/Applications.

## mysql

- [Location of my.cnf file on macOS](https://stackoverflow.com/questions/10757169/location-of-my-cnf-file-on-macos)

## jevn

### **列出已安装的Java版本：**

运行以下命令列出已安装的Java版本：

```shell
jenv versions
```

这会显示所有已安装的Java版本。

### 5. **设置全局Java版本：**

你可以选择设置一个全局的Java版本，该版本会被默认使用。运行以下命令：

```
env global <jdk_version>
```

将 `<jdk_version>` 替换为你想要设置为默认的Java版本。

### 6. **设置项目特定的Java版本：**

如果你想在特定的项目中使用不同的Java版本，可以进入到项目的目录，然后运行以下命令：

```
jenv local <jdk_version>
```

这会在项目目录下创建一个 `.java-version` 文件，指定项目特定的Java版本。

### 7. **验证Java版本：**

在终端中运行以下命令验证当前系统使用的Java版本：

```shell
java -version
```

确保输出显示的是你所期望的Java版本信息。

通过以上步骤，你可以使用 `jenv` 管理多个Java版本，并且在不同的项目中切换使用不同的Java版本。







Regenerate