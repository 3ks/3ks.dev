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

原文              |     机翻        |      建议        |
-----------------|-----------------|-----------------|
cluster          |     群集        |       集群      |
load balance     |     负载平衡     |    负载均衡      |
namespace        |     名称空间     |    命名空间      |
container image  |    容器图像      |   容器镜像       |

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


# 19533

feedback by [tengqm](https://github.com/tengqm) of [kuberntes](https://github.com/kubernetes) in [pr#19533](https://github.com/kubernetes/website/pull/19533)


### 术语

术语以大写字母开头，则视为术语，以小写字母开头，则翻译，如：

service 首字母小写时，是一般意义上的服务，需要翻译。首字母大写时，意指 Service 资源，不要翻译。

### 单词短语

原文                          |     译文             |       
-----------------------------|----------------------|
control loop                 | 控制环                |
service                      | 服务                  |
Service                      | Service              |
deployment                   | 部署                  |
Deployment                   | Deployment           |
find configured databases    | 查找所配置的数据库      |
manually change some settings| 手动修改某些配置        |

### 1

The Operator pattern captures how you can write code to automate a task beyond what Kubernetes itself provides.

~~Operator 模式捕获你是如何编写代码以实现自动化（Kubernetes 本身提供功能以外的）任务。~~

Operator 模式会封装您编写的（Kubernetes 本身提供功能以外的）任务自动化代码。

### 2

Kubernetes is designed for automation. Out of the box, you get lots of built-in automation from the core of Kubernetes.

~~Kubernetes 为自动化而生。开箱即用，您可以从 Kubernetes 核心中获得许多内置的自动化功能。~~

Kubernetes 为自动化而生。无需任何修改，您即可以从 Kubernetes 核心中获得许多内置的自动化功能。

### 3

simulating failure in all or part of your cluster to test its resilience

~~模拟整个或部分集群中的故障以测试其弹性~~

模拟整个或部分集群中的故障以测试其稳定性

### 4

choosing a leader for a distributed application without an internal member election process
  
~~在没有内部成员选举程序的情况下，为分布式应用选择 leader~~

在没有内部成员选举程序的情况下，为分布式应用选择首领角色

### 5

A Deployment that makes sure a Pod is running that contains the controller part of the operator.

~~确保 Pod 正在运行包含了 Operator 控制器部分的 Deployment。~~

一个包含 Operator 控制器部分的 Deployment，用来确保 Pod 处于运行状态。

### 6

Controller code that queries the control plane to find out what SampleDB resources are configured.

~~查询控制平面，找出那些配置 SampleDB 资源的控制器代码。~~

控制器代码，负责查询控制平面以找出已配置的 SampleDB 资源。

### 7

Because the Operator aims to provide robust automation for the resource it manages, there would be additional supporting code.

~~由于 Operator 旨在为其管理的资源提供强大的自动化功能，因此它还支持额外的代码。~~

由于 Operator 旨在为其管理的资源提供强大的自动化功能，因此它还需要一些额外的支持性代码。

### 8

you can run the controller in your cluster as a Deployment.

~~您可以在集群中将控制器作为部署运行。~~

您可以在集群中将控制器作为 Depoyment 来运行。

### 9

Once you have an Operator deployed, you'd use it by adding, modifying or deleting the kind of resource that the Operator uses. Following the above example, you would set up a Deployment for the Operator itself, and then:

~~部署 Operator 后，您可以对 Operator 使用的资源种类进行添加、修改或删除。按照上面的示例，您将为 Operator 本身设置一个部署，然后：~~

部署 Operator 后，您可以对 Operator 所使用的资源执行添加、修改或删除操作。按照上面的示例，您将为 Operator 本身建立一个 Deployment，然后：

### 10

&hellip;and that's it! The Operator will take care of applying the changes as well as keeping the existing service in good shape.

~~可以了！Operator 会负责应用更改并保持该 service 的良好状态。~~

可以了！Operator 会负责应用所作的更改并保持现有服务处于良好的状态。