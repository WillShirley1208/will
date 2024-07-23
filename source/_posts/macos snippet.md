---
title: macos snippet
date: 2021-12-20 11:25:00
tags: snippet
categories: macos
---

## PORT

- 查看指定进程pid的占用端口

  ```shell
  lsof -i -P -n | grep LISTEN | grep {pid}
  ```

  

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



## brew

- Disable  auto update when install 

  ```shell
  temp method 1:
  HOMEBREW_NO_AUTO_UPDATE=1 brew install <formula>
  
  temp method 2:
  export HOMEBREW_NO_AUTO_UPDATE=1
  
  permanently method:
  $ vim ~/.bashrc
  export HOMEBREW_NO_AUTO_UPDATE=1
  source ~/.bashrc
  or
  $ vim ~/.zshrc
  export HOMEBREW_NO_AUTO_UPDATE=1
  source ~/.zshrc
  ```

  





Regenerate