---
title: Network Manager 入门
thumbnail: ''
disqusId: e933125c-1232-4ee0-bfa9-c1669d7513cf
categories:
  - ["network","NetworkManager"]
tags:
  - NetworkManager
  - NM
date: 2020-08-25 17:00:31
---

Network Manager 入门笔记，完成基本的网络配置

<!-- more -->

## 介绍

`NetworkManager(NM)` 是 2004 年 Red Hat 启动的项目，旨在能够让 Linux 用户更轻松地处理现代网络需求，尤其是无线网络，能自动发现网卡并配置 IP 地址。

在 rhel8/centos8 上，network.service 已被废弃，我们只能通过 NetworkManager 进行网络管理。

当然，我们不需要直接使用 NetworkManger 管理网络，我们可以通过下面这些工具进行 NetworkManger 管理，他们类似于 NetworkManger 客户端：

- nmcli：命令行。这是最常用的工具，本文将详细讲解该工具使用。
- nmtui：在shell终端开启文本图形界面。示意图见本文最后的Tips
- Freedesktop applet：如GNOME上自带的网络管理工具
- cockpit：redhat 自带的基于 web 图形界面的管理工具，具有 dashborad 和基础管理功能。

本文只介绍 NetworkManager 相关知识以及 nmcli 的使用方法。

## 概念

在 NM 中，有两个主要对象：`设备`（device）和 `连接`（connection），它们是一对多的关系，即一个设备可以对应多个连接。但是，一个设备只能有一个活跃的连接，其它连接会变为非活跃、不可用。

### 设备（device）

我们可以通过命令 `nmcli d` 或 `nmcli device` 者查看当前设备状态：

```bash
$ nmcli d
DEVICE  TYPE      STATE      CONNECTION
ens33   ethernet  connected  ens33
lo      loopback  unmanaged  --
```

输出包含四列，分别表示：设备名、设备类型、状态、激活地连接名。

设备名和连接名可以相同，但他们是不同的对象，在后续的操作中注意不要被同名的情况误导。

`并非`所有的设备都会被 NM 接管，设备常见的状态有：

状态           |  描述   |
--------------|--------|
connected     | 已被 NM 接管，设备当前`有`活跃的连接   |
disconnected  | 已被 NM 接管，设备当前`没有`活跃的连接 |
unmanaged     | 未被 NM 接管，但可以通过命令接管该设备   |
unavailable   | 未被 NM 接管，不可用，NM 无法接管该设备，通常由网卡被 down 掉导致，例如：ip link set ethX down|

NM 只能管理 `已被 NM 接管` 设备的网络配置。

### 连接（connection）

我们可以通过命令 `nmcli c` 或 `nmcli connection` 者查看当前连接状态：

```bash
$ nmcli c
NAME   UUID                                  TYPE      DEVICE
ens33  d20368c4-4112-4773-b3a5-c8d06907c349  ethernet  ens33
```

输出包含四列，分别表示：连接名名、唯一标识 UUID、连接类型、对应的设备名。

连接的常见状态有：

状态           |  描述   |
--------------|--------|
connected     | 活跃的连接   |
disconnected  | 非活跃的连接 |

连接的状态由输出结果的颜色来判断，绿色为活跃，正常颜色为非活跃：

![](https://cdn.sguan.top/markdown/20200825165456.png)

你也可以执行 `nmcli device` 根据设备的状态及其对应的连接来判断连接的状态。

## 基本网络配置

想要正常访问互联网，需要配置：IP 配置方法（DHCP 或手动）、IP 地址、子网掩码、网关、DNS。而服务器一般使用静态 IP：

```bash
# 配置
nmcli c mod ens33 ipv4.method 'manual' ipv4.addr '192.168.7.10/24'  ipv4.gateway '192.168.7.2' ipv4.dns '8.8.8.8'
# 使更改生效
nmcli c up ens33
```

其中：

- 在 nmcli 中，很多关键字都支持缩写，c 是 connection 的缩写，mod 是 modify 的缩写，d 是 device 的缩写。
- 并不是每一个`连接`都要配置 DNS，因为默认情况下，有一个系统默认的全局 DNS（/etc/resolve.conf）。只有当你需要为这个`连接`指定特别的 DNS 时才需要配置。
- ipv4.addr 等属性列表，可以通过命令 `nmcli c show ens33` 查看，然后根据自己的需要进行修改。

## 创建连接

```bash
# 创建连接
nmcli c add type ethernet ifname ens33 con-name eth0 ipv4.method 'manual' ipv4.addr '192.168.7.11/24'  ipv4.gateway '192.168.7.2'
# 激活（切换）连接
nmcli c up eth0
```

其中：

- type ethernet 指定连接类型
- ifname ens33 指定对应的设备名
- con-name eth0 指定连接名，可以忽略，此时会自动生成连接名
- ipv4.method 等后续配置，可以在创建时就指定，也可以创建好链接后，再修改
- eth0 激活后，之前的 ens33 连接会变为非活跃状态
- 设备是否（例如开机）自动连接可以通过 `nmcli d set <ifname> [autoconnect yes|no] [managed yes|no]` 来配置

## 多个 IP

虽然一个设备只能有一个激活的连接，但这不代表只能有一个 IP，我们可以通过以下方式添加多个 IP

### 单连接多 IP

```bash
nmcli c mod eth0 +ipv4.addr '192.168.7.12/24'
nmcli c up eth0
```

注意，ipv4.addr 前面有一个 `+` 表示追加，对应的，还可以使用 `-` 表示移除，而无符号则表示覆盖

### 桥接

```bash
# 添加桥接连接
nmcli c add type bridge ifname eth1 con-name eth1
# 配置 IP
nmcli c mod eth1 ipv4.method 'manual' ipv4.addr '172.16.1.10/16'  ipv4.gateway '172.16.0.2'
# 激活
nmcli c up eth1
```

其中：

- type bridge 表示添加一个桥接连接，同时它会自动创建一个桥接设备，形成逻辑上的两个设备，这样我们就可以同时有两个活跃的连接

## 其它

### -h

nmcli 拥有很完整的帮助文档，在使用时你可以经常加上 `-h` 查看。简单来讲，主要就是 connection 和 device 对象的增删改查。

### 命令补全

nmcli 不仅支持命令缩写，还支持 tab 命令补全，前提是安装 `bash-completion`，可以通过如下命令安装它：

```bash
dnf install -y bash-completion
```

完成后，重新登录一下即可。

## 参考链接

[基于RHEL8/CentOS8的网络IP配置详解](https://mp.weixin.qq.com/s?__biz=MjM5NzAzMTY4NQ==&mid=2653932241&idx=1&sn=7af8ae2f6283fb67eb9e1cba1e6b277c)

[使用 NETWORKMANAGER 命令行工具 NMCLI](https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/7/html/networking_guide/sec-network_bridging_using_the_networkmanager_command_line_tool_nmcli) （由 RedHat 提供的文档，并且提供了中文版）
