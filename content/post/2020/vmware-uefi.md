---
title: "在 VMWare 中使用 UEFI 方式安装虚拟机"
date: 2020-02-22T17:05:36+08:00
tags: ["VMWare","UEFI","GPT"]
draft: false 

---

# VMWare 设置 UEFI 启动

[VMware Workstation虚拟机开启UEFI模式](https://www.glbwl.com/vmware-workstation-boot-uefi.html)

# 建立分区表

这一步 CentOS 的安装镜像会基于主板的引导方式自动选择。

Unknown 物理磁盘 -> GPT || MSDOS 分区表 

MSR 引导分区为 /boot，且需要为硬盘第一个分区

GPT 引导分区为 /boot/efi，同样需要该分区为第一个分区

# 性能测试

[sysbench 安装](https://blog.51cto.com/bilibili/2173243)
