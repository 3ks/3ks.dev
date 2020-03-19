---
title: "Install Kubernetes With Minikube"
date: 2020-03-15T22:34:45+08:00
tags: ["none",]
draft: true 

---

# Minikube

Minikube 用于安装一个单机的 k8s 集群，该集群仅用于入门学习 k8s 的一些特性，不适用于生产环境

# 安装

安装 minikube

安装 kubectl

minikube start --vm-driver=none

minikube status

# 参考链接

[Install Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)

[improve kubeadm preflight message for bridge-nf-call-iptables](https://github.com/kubernetes/kubeadm/issues/312)