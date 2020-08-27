---
title: "使 CentOS 支持 NTFS（弃用）"
date: 2020-02-22T17:00:13+08:00
tags: ["CentOS","NTFS"]
draft: false 

---

# 使 CentOS 支持 NTFS

- 下载组件

```bash
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum update
yum install ntfs-3g
安装完成后，正常挂载即可
特别注意：如果centos不支持USB3.0请使用2.0的接口，否则会报错！
```

- 挂载硬盘

```bash
mount -t ntfs-3g  /硬盘路径   /挂载路径
如：mount -t ntfs-3g /dev/sdc1 /mnt/usb 
卸载硬盘
umount  /挂载路径
如：umount /mnt/usb
```

- 开机自动挂载(硬盘路径应保持不变)

```bash
#备份配置文件
cp /etc/fstab  /etc/fatab.bak
#添加配置信息
vi /etc/fstab
文件尾添加
/dev/sdb1  /mnt/usb ntfs-3g  defaults 0 0
```

如果开机自动进入紧急模式(welcome to emergency mode! )，这可能由于找不到自动挂载的硬盘而导致的，可以参考 [这篇文章](https://www.linuxidc.com/Linux/2017-08/146347.htm)。
