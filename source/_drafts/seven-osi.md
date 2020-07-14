---
title: "Seven Osi"
date: 2020-03-07T11:07:48+08:00
tags: ["none",]
draft: true 

---

---
title: "草稿纸"
date: 2020-03-01T23:07:56+08:00
tags: ["none",]
draft: true 

---

# 五层模型

# 1 物理层

[TCP/IP（二）物理层详解](https://www.cnblogs.com/zhangyinhua/p/7607633.html)

[802.1X协议的工作机制流程详解](https://blog.csdn.net/phunxm/article/details/9389661)

[IEEE 802.2](https://en.wikipedia.org/wiki/IEEE_802.2) 

# 2 数据链路层

# 2.1 二层交换机

# 2.2 MAC

# 3 网络层

### 3.1 ARP

[ARP原理](https://blog.csdn.net/mariofei/article/details/23599579)

### 3.2 路由器

### 3.3 三层交换机

# 4 传输层

### 4.1 TCP 状态机

[TCP状态机详解](https://blog.csdn.net/u012503786/article/details/78647661)

### 4.2 拥塞控制算法

[TCP BBR拥塞控制算法解析](https://blog.csdn.net/ebay/article/details/76252481)

### 4.3 丢包重传

[tcp/ip 上，丢包重传机制](https://blog.csdn.net/jav_imba/article/details/52777946)

### 5 应用层

### 5.1 DNS

[DNS解析过程详解](https://blog.csdn.net/crazw/article/details/8986504)

### 5.2 HTTP

[一文读懂 HTTP/2 及 HTTP/3 特性](https://blog.fundebug.com/2019/03/07/understand-http2-and-http3/)

### 5.3 SSH

### 5.4 Websocket

# 6 扩展

QUIC

TLS

[OSI 七层模型和 TCP/IP 四层模型](https://blog.csdn.net/freeking101/article/details/77977941)

[网络7层协议，4层，5层？理清容易混淆的几个概念](https://blog.csdn.net/cc1949/article/details/79063439)

[二进制与字符编码](https://www.cnblogs.com/hellojesson/p/5793591.html)

[计算机中二进制数据的编码方式](https://blog.csdn.net/dongbaoming/article/details/60142163)

# 网络工程

[CCNA 知识点总结](https://wenku.baidu.com/view/42856c8ba0116c175f0e4863.html)

静态路由

IGRP/EIGRP 协议

RIPv1、RIPv2；RIP、OSPF；

BGP

IPv6

矢量路由、链路状态路由；

有类路由、无类路由；


DR、BDR选举；

STP 生成树、STP/RSTP/MSTP；

trunk

VTP 三种模式

NAT、PAT 过程

DHCP 过程

帧中继

```text
在 OSI 模型 ARP 协议属于链路层
而在 TCP/IP 模型中，ARP 协议属于网络层
```

# 操作系统

[操作系统面试知识点总结](https://blog.csdn.net/sunxianghuang/article/details/51883496)

[操作系统知识点整理](https://github.com/Aifeng1214/CS-knowledge-review/blob/master/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E7%9F%A5%E8%AF%86%E7%82%B9%E6%95%B4%E7%90%86.md)

[操作系统的知识点整理（另一个）](https://juejin.im/post/5d95e3ab6fb9a04e20416d37)

硬件：CPU、内存、硬盘、显卡、网卡

操作系统引导，初始化

进程、线程、死锁

存储管理：内存、硬盘、文件系统、网络文件系统

数据库

监控、追踪、分布式

命名空间、资源隔离、容器技术

[Linux Namespace : 简介](https://www.cnblogs.com/sparkdev/p/9365405.html)

[Docker基础: Linux内核命名空间之（6）user namespace](https://blog.csdn.net/liumiaocn/article/details/52549978)

[Linux命名空间入门（一） UTS命名空间](https://blog.csdn.net/weifenghai/article/details/52836109?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task) 

bash、Makefile、CI/CD

SSH 免密登录

# 数据库

范式
表结构、索引、优化
B+ 树
引擎
SQL 语句、复杂语句
Nosql
NFS
Ceph
PromeQL
GraphQL
TiDB

# 协作

Git
GitHub
GitLab
Markdown
Swagger

# RESTful

token 身份认证 
API 交互

# 容器

Docker
Kubernetes
Prometheus
Grafana
CoreDNS
Istio

# Golang

语法、高级特性

单元测试

# 搭建服务器相关

购买域名
域名解析
申请 SSL 证书
服务器系统
网关
CDN 加速

# 服务器加速

原版：BBR
建站：TCPA 需定制内核
带宽：BBR PLUS 需定制内核
买 CN2 GIA-E？
试试 trojan？

# 路线流程

- 安装 proxmox 
- 基于 cloud-init 快速初始化多个节点
- 基于 [kubespray](https://github.com/kubernetes-sigs/kubespray#requirements) 快速安装安装 k8s 高可用集群

# 还不知道怎么弄的下一步

- 快速安装 Istio
- 自动部署项目
- 快速安装一堆组件，监控，可视化，日志等

# 需要持久化的内容

- 各种数据（数据库/缓存/网络文件系统等）
- 各种文件（软件包/镜像等），以加快二次下载速度（是否可行？）
- 项目内容（代码/配置/环境变量/脚本等），放在代码仓库

# 目标

全流程自动化（节点 -> k8s 集群 -> Istio -> 项目）

# 问题记录

- 未订阅的情况下，proxmox 每个虚拟机只能有一个备份
