---
title: "CentOS 修改主机名"
date: 2020-02-22T16:38:05+08:00
tags: ["CentOS","hostname"]
draft: false 

---

# 修改主机名

直接编辑 `/etc/hostname` 文件即可修改主机名。

# 修改并立即生效

但是，上面的方法需要重启计算机才能使修改生效。

通过命令 `hostnamectl` 修改主机名可以立即生效。

```bash
hostnamectl set-hostname your-hostname
```

# 参考链接

[centos修改主机名的正确方法](https://www.cnblogs.com/zhaojiedi1992/p/zhaojiedi_linux_043_hostname.html)
