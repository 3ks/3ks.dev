---
title: CentOS 分配和禁用 swap 空间
thumbnail: 'https://cdn.sguan.top/makrdown/20200714231024.png'
disqusId: d3fa0ef6-c5de-11ea-87d0-0242ac130003
categories:
  - ["Linux","CentOS","swap"]
tags:
  - Linux
  - CentOS
  - swap
date: 2020-07-14 22:25:40
---

云服务的内存不够，也没有自动分配 swap 空间，导致 mysql 服务器无法启动。

此时我们可以自己分配 swap 空间作为缓存，个人感觉 swap 类似于 windows 里的虚拟内存。

<!-- more -->

## 分配并启用 swap

### 获取硬盘设备路径

```bash
$ df -h
```

### 分配空间4G

```bash
$ sudo dd if=/dev/vda1 of=/swapfile count=4096 bs=1MiB
```

DO 文档里面的 `sudo fallocate -l 4G /swapfile` 是错误的，评论区出人才！

### 权限设置

```bash
$ sudo chmod 600 /swapfile
$ sudo mkswap /swapfile
```

制作 swap 系统，如果成功会有一个类似下面的提示：

```bash
Setting up swapspace version 1, size = 4194300 KiB
no label, UUID=b99230bb-21af-47bc-8c37-de41129c39bf
```

### 启动 swap

```bash
$ sudo swapon /swapfile
```

### 查看结果

```bash
free -m
```

检查 swap 是否已经在运行、有容量了。

### 开机自动挂载

在文件 `/etc/sysctl.conf` 末尾追加内容、并保存

{% codeblock "/etc/sysctl.conf" lang:conf %}
/swapfile   swap    swap    sw  0   0
{% endcodeblock %}

## 禁用系统默认的 swap 分区

某些场景下，不能使用 swap 分区，此时则应该禁用 swap 分区：

### 取消挂载

编辑 `/etc/fstab` 文件，取消 swap 分区的挂载：

{% codeblock "/etc/fstab" lang:text %}
# /etc/fstab
# Created by anaconda on Sat Feb 29 05:30:30 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=666d6259-55da-4501-91fb-40b2c0b16ca2 /boot                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
{% endcodeblock %}

`swap` 文件系统那一行对应的路径就是目前挂载的 swap 分区。

我们将这一行删掉，或者注释掉，保存退出。

下次系统启动时就不会自动挂载该 swap 分区。

如果不想重启系统，根据 swap 分区的路径，执行下面的命令即可立即禁用 swap 分区。

```bash
$ swapoff /dev/mapper/centos-swap
```

### 释放分区

todo 

## 参考链接

[How To Add Swap on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-centos-7)

[CentOS 7 关闭 swap 分区](https://blog.csdn.net/dejunyang/article/details/97972399)