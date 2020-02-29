---
title: "CentOS 分配和禁用 swap 空间"
date: 2020-02-22T15:54:45+08:00
tags: ["CentOS","swap","memory"]

---

# 背景

云服务内存不够，也没有自动分配 swap 空间，导致mysql服务器无法启动。

此时我们可以自己分配 swap 空间作为缓存，感觉 swap 类似于 windows 里的虚拟内存。

# 步骤

- df -h

> 获取硬盘设备路径

- sudo dd if=/dev/vda1 of=/swapfile count=4096 bs=1MiB

> 分配空间4G
> 官方的 `sudo fallocate -l 4G /swapfile` 是错误的，评论区出人才！

- sudo chmod 600 /swapfile

> 权限设置

- sudo mkswap /swapfile

> 制作 swap 系统，如果成功会有一个类似下面的提示：

```bash
Setting up swapspace version 1, size = 4194300 KiB
no label, UUID=b99230bb-21af-47bc-8c37-de41129c39bf
```

- sudo swapon /swapfile

> 启动swap

- free -m

> 可以看到swap是否已经在运行了

- sudo vi /etc/sysctl.conf

> 在文件末尾追加内容

```bash
/swapfile   swap    swap    sw  0   0
```

- 保存退出即可。

# 禁用系统默认的 swap 分区

1. 查看当前挂载 swap 的路径

vim /etc/fstab
 
```bash
# /etc/fstab
# Created by anaconda on Sat Feb 29 05:30:30 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=666d6259-55da-4501-91fb-40b2c0b16ca2 /boot                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
```

swap 文件系统那一行对应的路径就是目前挂载的 swap 分区。

我们将这一行删掉，或者注释掉，然后 wq 保存退出。

这样下次系统启动时就不会自动挂载该 swap 分区。

不想重启系统的同学记住刚刚 swap 分区的路径，执行下面的命令即可立即禁用 swap 分区。

```bash
swapoff /dev/mapper/centos-swap
```

todo: 分区释放 

# 参考链接

[How To Add Swap on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-centos-7)

[CentOS 7 关闭 swap 分区](https://blog.csdn.net/dejunyang/article/details/97972399)