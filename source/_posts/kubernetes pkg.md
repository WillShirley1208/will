---
title: Kubernetes pkg
date: 2024-12-17 10:47:36
tags: pkg
categories: Kubernetes
---

# klog

- `klog.Info` is equivalent to `klog.V(0).Info`.
- For more detailed debug or trace messages, use klog.V(level).Info, where level is greater than 0 (e.g., klog.V(2).Info).

