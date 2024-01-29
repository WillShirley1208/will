---
title: python problems
date: 2023-11-14 15:28:51
tags: problems
categories: python
---

-  NotOpenSSLWarning: urllib3 v2.0 only supports OpenSSL 1.1.1+, currently the 'ssl' module is compiled with 'LibreSSL 2.8.3'.

  ```shell
  pip uninstall urllib3
  pip install 'urllib3<2.0'
  ```

  [stackoverflow](https://stackoverflow.com/questions/76187256/importerror-urllib3-v2-0-only-supports-openssl-1-1-1-currently-the-ssl-modu)

## 依赖

- paddlepaddle依赖只能在 python 3.9版本之内可以安装

