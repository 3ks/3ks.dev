---
title: 使用 kubeadm 安装 kubernetes
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2020-09-12 17:42:20
---

一篇船新的博客

<!-- more -->

## 安装 docker

## 安装二进制文件

## init 集群

> 需注意 CNI 插件 CIDR

https://docs.projectcalico.org/getting-started/kubernetes/quickstart


sudo kubeadm init --pod-network-cidr=172.16.0.0/16 --control-plane-endpoint "192.168.7.19:6443" --upload-certs
sudo kubeadm init --pod-network-cidr=172.16.0.0/16 --control-plane-endpoint "34.92.172.84:6443" --upload-certs

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config


## 安装 CNI 插件

kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml

wget https://docs.projectcalico.org/manifests/custom-resources.yaml

vim custom-resources.yaml

kubectl create -f custom-resources.yaml

watch kubectl get pods -n calico-system

kubectl get nodes -o wide

## 加入节点

### Master

kubeadm join 192.168.7.19:6443 --token cqyuw9.vaehthtdbk40z9oy \
    --discovery-token-ca-cert-hash sha256:f842ca97e94c3e6733d3265bf16ab310ad0006cea672fe1b2de66f60cde746f7 \
    --control-plane --certificate-key 0ccb432a8d097d0792ab46c1e901ef615d59e0fcb09f0cb05fb3cab01b1e2e0c
    
### Worker

kubeadm join 192.168.7.19:6443 --token cqyuw9.vaehthtdbk40z9oy \
    --discovery-token-ca-cert-hash sha256:f842ca97e94c3e6733d3265bf16ab310ad0006cea672fe1b2de66f60cde746f7


## Hello World

#########################


## 高可用 kubernetes

### 控制平面

### 节点

## 存储

## 网络

## 策略

## 数据库

## 日志

## 监控

## 通知

## cert-manager 和 ingress

## 一个代码片段

{% codeblock "main.go" lang:go https://www.google.com 查看源文件 %}

{% endcodeblock %}

## 参考链接

[name](https://github.com/3ks)