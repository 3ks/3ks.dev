---
title: "CentOS 网卡初始化"
date: 2020-02-22T16:19:32+08:00
tags: ["CentOS","Network","IP"]
draft: false 

---

# 启用 etho0

CentOS 在刚刚装装好的时候 `可能` 存在没有启用网卡的情况，这个时候我们需要手动添加一个网卡,步骤如下：

- cd /etc/sysconfig/network-scripts/
- cp ifcfg-ens33 ens33_bak
- mv ifcfg-ens33 ifcfg-eth0
- vi ifcfg-eth0   

修改`name`=`eth0`

修改`device`=`eth0`

修改`ONBOOT`=`YES`

- vi /etc/default/grub

在`GRUB_CMDLINE_LINUX`原有参数后添加 `net.ifnames=0 biosdevname=0`

- grub2-mkconfig   -o  /boot/grub2/grub.cfg
- reboot
- ip addr

![成功获取IP](https://cdn.sguan.top/markdown/20181121/3oXaX3jtuKGw.png?imageslim)

# 配置静态 IP

如果需要配置静态IP，可以编辑 `ifcfg-eth0` 文件:

- vi /etc/sysconfig/network-scripts/ifcfg-eth0

```bash
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="static"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPADDR=192.168.7.7  #IP地址
NETMASK=255.255.255.0 #子网掩码
BROADCAST=192.168.7.255 #广播地址
GATEWAY=192.168.7.2 #网关
HWADDR="00:0C:29:8A:10:DB" #MAC地址,必须与虚拟机提供的MAC相同
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="eth0"
UUID="2d92e919-5fdc-4e73-ba32-d61876f172d4"
DEVICE="eth0"
ONBOOT="yes"
IPV6_PRIVACY="no"
```

- systemctl restart network 

重启网络，查看IP

> 如果出现Failed to start LSB: Bring up/down networking 的问题，可能是network与NetworkManager冲突导致的，禁用NetworkManager即可。

- systemctl stop NetworkManager
- systemctl disable NetworkManager
- reboot

# 配置 DNS

如果没有DNS，电脑也是没法正常上网的

- vim /etc/NetworkManager/NetworkManager.conf

```bash
#在main部分添加 “dns=none” 选项，类似下面这样
[main]
plugins=ifcfg-rh
dns=none
```

- vim /etc/resolv.conf

根据自己的地区选择合适的DNS

```bash
nameserver 8.8.8.8
nameserver 61.139.2.69
```

### 参考链接

[CentOS7 Failed to start LSB: Bring up/down networking.解决方法](https://www.cnblogs.com/bonjov1/p/4323836.html)