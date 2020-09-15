---
title: kube-draft
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2020-09-13 21:20:33
---

一篇船新的博客

<!-- more -->

## 使用 Calico 作为 CNI 插件

[Quickstart for Calico on Kubernetes](https://docs.projectcalico.org/getting-started/kubernetes/quickstart)

## Kubernetes 里的三种 IP

- Node IP
- Cluster IP
- Pod IP

[k8s-集群里的三种IP（NodeIP、PodIP、ClusterIP）](https://blog.csdn.net/qq_21187515/article/details/101363521)

##  查看集群 CIDR

Cluster IP CIDR

```bash
$ kubectl cluster-info dump | grep -m 1 cluster-cidr 
```

Pod IP CIDR

```bash
$ kubectl cluster-info dump | grep -m 1 cluster-cidr
```

[How do you find the cluster & service CIDR of a Kubernetes cluster?
](https://stackoverflow.com/questions/44190607/how-do-you-find-the-cluster-service-cidr-of-a-kubernetes-cluster)

## Kubernetes 接口

Kubernetes 有三个主要的接口：CRI、CNI、CSI

CRI：Container Runtime Interface，容器运行时接口

CNI：Container Network Interface，容器网络接口

CSI：Container Storage Interface，容器存储接口

[Kubernetes中的开放接口CRI、CNI、CSI](https://zhuanlan.zhihu.com/p/33390023)

[扩展 Kubernetes 之 CRI](https://zhuanlan.zhihu.com/p/33390023)

## 设置节点 role

其实只是一个特殊的 label，与调度无关：

```bash
$ kubectl label node <node-name> node-role.kubernetes.io/worker=worker
```

[k8s设置node节点Role属性](https://docs.lvrui.io/2018/11/01/k8s%E8%AE%BE%E7%BD%AEnode%E8%8A%82%E7%82%B9Role%E5%B1%9E%E6%80%A7/)

## 访问 Kubernetes 服务的几种方式

集群内部一般使用 ClusterIP 类型的 Service

外部一般使用 NodePort、LoadBalancer 类型的 Service，对于 HTTP/HTTPS 服务还可以使用 Ingress

[Kubernetes的三种外部访问方式：NodePort、LoadBalancer 和 Ingress](http://dockone.io/article/4884)

[浅析从外部访问 Kubernetes 集群中应用的几种方式](https://juejin.im/post/6844903654391021581)

## 修改 helm 部署的应用

helm 主要用于快速部署应用，在修改方面的支持比较欠缺（而 Operator 就补足了这块），最直接的方式是直接修改 kubernetes 中的相关资源。

[Helm部署的服务如何修改配置](https://blog.csdn.net/boling_cavalry/article/details/105004586)

## traefik

[https://docs.traefik.io/](https://docs.traefik.io/)

## 删除 namespace 卡住

https://www.ichenfu.com/2019/02/20/kubernetes-namespaces-stuck-in-terminating-state/

## kubectl 手册

[kubectl commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)

## kubectl 备忘单

[kubectl 备忘单](https://kubernetes.io/zh/docs/reference/kubectl/cheatsheet/#kubectl-%E8%87%AA%E5%8A%A8%E8%A1%A5%E5%85%A8)

## 概念

### 分组和选择

- Labels
- Selector

### 配置 

- ConfigMap 
- Secrets
- Resource Quota

## 存储

- 持久卷声明：Persistent Volumes Claim
- 持久卷：Persistent Volumes 
- 存储类：Storage Class

### 工作负载

- 最小调度单元：Pod
- 无状态部署：Deployment
- 有状态部署：StatefulSet
- 守护部署：DaemonSet
- 任务：Job、Cron Job

### 通信

- Ingress  
- 服务：Service（ClusterIP、NodePort、LoadBalancer）
- 端点：Endpoint、Endpoint Slice

### 授权

- Service Account
- Role
- Role Binding

### CRD

- CRD
- Operator

[Kubernetes，2020 快速入门](https://zhuanlan.zhihu.com/p/100644716)
