---
title: "CentOS 修改时区"
date: 2020-02-22T16:42:00+08:00
tags: ["CentOS","timezone"]
draft: false 

---

# 修改时区

如果需要修改时区，CentOS7 中引入了一个 timedatectl 工具，直接运行命令：

`timedatectl set-timezone  Asia/Shanghai`

也可以手动修改时区文件 

`cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime`

> 如果设置时区后，时间依然快了8小时,可以执行下面的命令

`ln -s -f /usr/share/zoneinfo/Universal /etc/localtime`

# 参考链接

[CentOS 7 修改系统默认时区](https://my.oschina.net/imhuayi/blog/800202)

[CentOS 7系统时间与实际时间差8个小时](https://blog.csdn.net/lin521lh/article/details/78456654)
