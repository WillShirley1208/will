---
title: GPU learn
date: 2024-03-08 10:08:49
tags: learn
categories: GPU
---

# 命令

- 查看卡的数量

  `nvidia-smi -L`

- 查看显卡上运行的程序，以及所占内存情况

  `nvidia-smi --query-compute-apps=pid,process_name,used_memory,gpu_uuid --format=csv`

- 查看显卡所剩内存

  `nvidia-smi --query-gpu=memory.free --format=csv,noheader,nounits`

  