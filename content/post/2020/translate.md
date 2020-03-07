---
title: "Translate"
date: 2020-03-07T10:58:51+08:00
tags: ["none",]
draft: false 

---

# 建议词汇表

记录了一些在翻译过程中遇到的一些错误


# 机器翻译注意单词

不代表机翻是错误的，只是人们习惯的叫法不同。

本就是一个学习的过程，借助机翻不可怕，尽信机翻才可怕。

原文           |     机翻        |      建议        |
--------------|-----------------|-----------------|
cluster        |     群集        |       集群      |
load balance   |     负载平衡     |    负载均衡      |
namespace      |    名称空间      |    命名空间      |

# 19492

feedback by [tengqm](https://github.com/tengqm) of [kuberntes](https://github.com/kubernetes) in [pr#19492](https://github.com/kubernetes/website/pull/19492)

### 单词短语

原文                   |     译文             |       
-----------------------|--------------------|
front-proxy            | 前端代理             |
front-end proxy        | 前端代理             |
path                   | 路径                |
Default CN             | 默认 CN             |
Parent CA              | 父级 CN             |
O (in Subject)         | O (位于 Subject 中) |
hosts (SAN)            | 主机 (SAN)          |
recommended key path   | 建议的密钥路径        |
recommended cert path  | 建议的证书路径        |
recommended cert path  | 建议的证书路径        |
key argument           | 密钥参数             |
cert argument          | 证书参数             |
private key path       | 私钥路径             |
public key path        | 公钥路径             |
credential name        | 凭据名称             |
cert/key pair          | 证书/密钥偶对         |

###### 2

How certificates are used by your cluster

~~集群使用了多少证书~~

集群是如何使用证书的

> how 不是 how many

###### 3

etcd also implements mutual TLS to authenticate clients and peers.

~~etcd 还实现了双向 TLS 来对客户端和对端进行身份验证。~~

etcd 还实现了双向 TLS 来对客户端和对其他对等节点进行身份验证。

###### 4 

any other IP or DNS name you contact your cluster on (as used by [kubeadm][kubeadm] the load balancer stable IP and/or DNS name, `kubernetes`, `kubernetes.default`, `kubernetes.default.svc`,
`kubernetes.default.svc.cluster`, `kubernetes.default.svc.cluster.local`)

~~连接到集群不同的 IP 或 DNS 名（[kubeadm][kubeadm] 负载均衡使用的固定 IP 或 DNS，`kubernetes`、`kubernetes.default`、`kubernetes.default.svc`、`kubernetes.default.svc.cluster`、`kubernetes.default.svc.cluster.local`）~~

用来连接到集群的不同 IP 或 DNS 名（就像 [kubeadm][kubeadm] 为负载均衡所使用的固定 IP 或 DNS 名，`kubernetes`、`kubernetes.default`、`kubernetes.default.svc`、`kubernetes.default.svc.cluster`、`kubernetes.default.svc.cluster.local`）

###### 5

Hosts/SAN listed above are the recommended ones for getting a working cluster; if required by a specific setup, it is possible to add additional SANs on all the server certificates.

~~上面列出的 Hosts/SAN 是推荐的获取工作集群方式；如果需要特殊的安装，则可以在所有服务器证书上添加其他 SAN。~~

上面列出的 Hosts/SAN 是推荐的配置方式；如果需要特殊安装，则可以在所有服务器证书上添加其他 SAN。