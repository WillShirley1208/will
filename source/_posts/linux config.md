---
title: linux config
date: 2015-01-16 19:01:30
tags: config
categories: linux
---

### java config

- Use `java -XshowSettings:properties` to show the java.library.path (and others) value.

### native lib config
- `/lib` contain "essential" libraries that may be needed even in single-user (admin-only) mode and without /usr mounted
- `/usr/lib` are the libraries for the normal user-programs, that mostly can be found under /usr.
- `/usr/local/lib` are the libraries for locally installed programs and packages ie. things you've compiled and installed from source-packages yourself.

### other

- `/etc/environment`是设置整个系统的环境，而`/etc/profile`是设置所有用户的环境 