---
title: "HTTP 流量授权"
date: 2020-04-07T20:48:15+08:00
tags: ["none",]
draft: true 

---

# 总览

从在浏览器输入域名，到浏览器完成渲染，需要经历哪些环节。每个环节再延申讨论一下。

## 从浏览器到达服务器

这一部分过程基本没有多大变化，只是建立链接的过程有所不同。

## 从服务器收到请求到返回

从客户端请求进入网关开始，这一部分要聊的内容太多了，服务器架构在不断的演进中。

### 传统部署方式

### 传统大型网络

### 传统虚拟机部署

### 微服务部署

### 容器部署

### 容器编排部署

### 服务网格部署

Ingress Gateway -> Gateway
Virtual Service -> Service
Deployment -> Pod

# 调度

CPU 调度算法、Go 调度器（GMP）、kubernetes 调度器（标签、污点、容忍）

# 内存管理

# 存储

硬盘、分区、LVM 卷管理、LVM thin p...、文件系统、RAID 0 1 5 10、NFS、Ceph、PV、PVC

# 数据

Mysql、Redis、集群、主从、原生 SQL。

# 安全

PKI 证书、SSH 免密登录（公私钥）、TLS 安全传输（公私钥，怎么保证安全）、kubernetes 证书、Istio 证书

# 通信
 
TCP 三次握手、四次挥手。五次握手和七次握手、TCP 状态机

HTTP/1、HTTP/1.1、SPDY、HTTP/2、QUIC、HTTP/3

DNS、Search Domain、CoreDNS、CNI 的实现方式、优雅的解决网络障碍。

# bug

世间哪有完美的程序，我平时在使用过程中其实经常会遇到一些bug，即使是 kubernetes 这种顶级项目也不断有 bug 出现。

- B 站登录界面，QQ 快捷登录，指标不够人性化
- 微信有些未读消息（通知类）显示不出来，需要杀掉后台，再打开
- 腾讯 TIM 出现丢消息情况，我晚上从电脑发送到手机的消息，第二天早上电脑上就找不到这条记录了
