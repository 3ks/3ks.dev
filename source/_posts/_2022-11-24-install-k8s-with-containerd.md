---
title: install-k8s-with-containerd
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2021-03-07 00:45:10
---

最后一步 controller 与 worker 节点操作不同，前面的步骤都是通用的，两种节点都需要执行。

<!-- more -->

## 参数配置

{% codeblock "bash" lang:bash >folded %}
$ swapoff -a
$ systemctl disable --now ufw

$ cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

$ sudo modprobe overlay

$ sudo modprobe br_netfilter

$ cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

$ sudo sysctl --system
{% endcodeblock %}

## 安装 Containerd

{% codeblock "bash" lang:bash >folded %}
# 获取最新版本下载 URL
$ curl https://api.github.com/repos/containerd/containerd/releases/latest |jq -r '.assets[].browser_download_url'
https://github.com/containerd/containerd/releases/download/v1.6.10/containerd-1.6.10-linux-amd64.tar.gz
https://github.com/containerd/containerd/releases/download/v1.6.10/containerd-1.6.10-linux-amd64.tar.gz.sha256sum
https://github.com/containerd/containerd/releases/download/v1.6.10/containerd-1.6.10-linux-arm64.tar.gz
https://github.com/containerd/containerd/releases/download/v1.6.10/containerd-1.6.10-linux-arm64.tar.gz.sha256sum

# 下载需要的包
$ wget https://github.com/containerd/containerd/releases/download/v1.6.10/containerd-1.6.10-linux-amd64.tar.gz

# 解压到 PATH
$ tar Czxvf /usr/local/ containerd-1.6.10-linux-amd64.tar.gz
{% endcodeblock %}

配置 containerd.service

{% codeblock "bash" lang:bash >folded %}
# 下载 service 文件
$ wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service

# 创建并复制目录
$ mkdir -p /usr/local/lib/systemd/system
$ cp containerd.service /usr/local/lib/systemd/system

# reload service
$ systemctl daemon-reload
$ systemctl enable --now containerd
{% endcodeblock %}

## 安装 runc

{% codeblock "bash" lang:bash >folded %}
# 获取最新版本下载 URL
$ curl https://api.github.com/repos/opencontainers/runc/releases/latest |jq -r '.assets[].browser_download_url'
https://github.com/opencontainers/runc/releases/download/v1.1.4/runc.amd64

# 下载需要的包
$ wget https://github.com/opencontainers/runc/releases/download/v1.1.4/runc.amd64

# 复制到 PATH
$ chmod 755 runc.amd64
$ cp runc.amd64 /usr/local/bin/runc
{% endcodeblock %}

## 安装 crictl

{% codeblock "bash" lang:bash >folded %}
# 获取最新版本下载 URL
$ curl https://api.github.com/repos/kubernetes-sigs/cri-tools/releases/latest |jq -r '.assets[].browser_download_url'
https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.25.0/crictl-v1.25.0-linux-amd64.tar.gz
https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.25.0/crictl-v1.25.0-linux-amd64.tar.gz.sha256
https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.25.0/crictl-v1.25.0-linux-amd64.tar.gz.sha512


# 下载需要的包
$ wget https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.25.0/crictl-v1.25.0-linux-amd64.tar.gz

# 解压复制到 PATH
$ tar zxvf crictl-v1.25.0-linux-amd64.tar.gz
$ cp crictl /usr/local/bin/
{% endcodeblock %}

## 安装集群

### 安装 Kubelet&kubeadm&kubectl

{% codeblock "bash" lang:bash >folded %}
$ sudo apt-get update
$ sudo apt-get install -y apt-transport-https ca-certificates curl
$ sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
$ echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
$ sudo apt-get update
$ sudo apt-get install -y kubelet kubeadm kubectl
$ sudo apt-mark hold kubelet kubeadm kubectl
{% endcodeblock %}

### controller 节点

> 在 controller 节点对集群进行初始化

{% codeblock "bash" lang:bash >folded %}
$ kubeadmin init
{% endcodeblock %}

init 支持很多参数，常见的 `--control-plane-endpoint`、`--pod-network-cidr`、`--apiserver-advertise-address` 等参数按需指定。

命令完成后，会自动生成加入集群的命令，其中也包含了 token。

集群初始化后，还不能使用，还需要选择安装一款 CNI 插件。

> 安装 CNI，只有 controller 节点需要安装 CNI，worker 节点不需要安装。

这里选择的是 `cilium`，首先安装 `cilium-cli`

{% codeblock "bash" lang:bash >folded %}
# 获取最新版本下载 URL
$ curl https://api.github.com/repos/cilium/cilium-cli/releases/latest |jq -r '.assets[].browser_download_url'
https://github.com/cilium/cilium-cli/releases/download/v0.12.9/cilium-darwin-amd64.tar.gz
https://github.com/cilium/cilium-cli/releases/download/v0.12.9/cilium-darwin-amd64.tar.gz.sha256sum
https://github.com/cilium/cilium-cli/releases/download/v0.12.9/cilium-darwin-arm64.tar.gz
https://github.com/cilium/cilium-cli/releases/download/v0.12.9/cilium-darwin-arm64.tar.gz.sha256sum


# 下载需要的包
$ wget https://github.com/cilium/cilium-cli/releases/download/v0.12.9/cilium-darwin-amd64.tar.gz

# 解压复制到 PATH
$ tar zxvf cilium-darwin-amd64.tar.gz
$ cp cilium /usr/local/bin
{% endcodeblock %}

再通过 cilium-cli 安装 cilium CNI

{% codeblock "bash" lang:bash >folded %}
$ cilium install
{% endcodeblock %}

### worker 节点

worker 节点直接使用 kubeadm join，直接复制粘贴 controller 节点初始化时给出 url 即可。

{% codeblock "bash" lang:bash >folded %}
$ kubeadm join 192.168.7.21:6443 --token wvku0h.rulblno8in3wx58s --discovery-token-ca-cert-hash sha256:d094f790d0374470f32fb1ecaea12e7c8d7dc3087fdec6fe2eeac8d085c5994e
{% endcodeblock %}

## 参考链接

[基于docker部署skywalking实现全链路监控](https://www.cnblogs.com/xiao987334176/p/13530575.html)

[SkyWalking OAP](https://hub.docker.com/r/apache/skywalking-oap-server)

[Current image doesn't Elasticsearch 6](https://github.com/apache/skywalking/blob/master/docs/en/setup/backend/backend-storage.md#elasticsearch)