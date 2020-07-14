---
title: "初始化 kubernetes"
date: 2020-02-22T17:30:56+08:00
tags: ["kubernetes","k8s"]
draft: true 

---

# 1 环境信息

本文将从 0 开始安装部署一个简单的 K8S 集群，相关信息如下：
- Master 节点：1个
- Node 节点：2个
- 每个节点使用 CentOS7 操作系统
- 使用 kuberadm 工具安装 K8S 集群

# 2 节点配置

k8s 对于环境有一些要求，虽然可以通过参数解决跳过限制，但最好依照官方的要求修改系统配置。这里只列出我配置过的地方，如果你在安装过程中出现其它错误，可以参考官方文档获取完整配置要求 [Installing kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

## 2.1 网络配置

- 节点网络使用 172.16.0.0/16

## 2.2 禁用 Swap 交换分区

内存不够用了，请加内存条，别用硬盘来忽悠！

```bash
swapoff -a
vim /etc/fstab
# 使用 # 将带有 swap 字样的挂载行注释掉
```

## 2.3 禁用 SeLinux 

```bash
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
```

## 2.4 禁用 Firewalld 或开放指定端口

- 禁用 Firewalld

```bash
systemctl disable --now firewalld
```

- 开放端口

![mark](https://cdn.sguan.top/markdown/20191007/ejAdzY6pjM9M.png?imageslim)

上图中是 k8s 默认使用的端口列表，其中带 * 星号的端口为可配置端口，可以自行手动配置防火墙手动开启。

## 2.5 iptables 

bridge-nf-call-iptables 和 bridge-nf-call-ip6tables 文件值需要为 1，确保 k8s 维护的路由表得以生效。

```bash
sysctl net.bridge.bridge-nf-call-iptables=1

cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system

echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables && echo '1' > /proc/sys/net/bridge/bridge-nf-call-ip6tables
```

## 2.6 安装工具

每个节点需要安装以下工具：
- docker-ce (v19.03.2)
- kubelet (v1.16.1)
- kubeadm (v1.16.1)
- kubectl (v1.16.1)

#### 2.6.1 添加软件源

```bash
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```

#### 2.6.2 执行命令
```bash
yum install -y kubelet kubeadm kubectl docker-ce --disableexcludes=kubernetes
systemctl enable --now kubelet
```

> Master 节点与 Node 节点都需要安装上面的工具，只是在初始化时，方式不一样。

# 3 初始化 Master 节点

## 3.1 初始化

执行命令：

```bash
kubeadm  init --pod-network-cidr=10.244.0.0/16
```

![mark](https://cdn.sguan.top/markdown/20191007/hv2kEYMUOo58.png?imageslim)

安装成功后，可以看到类似于上图所示的信息，这里的每句话都很重要：

- `successfully` 通过该字眼，表示 k8s 已经安装成功

- `To start using your cluster……` 以及后面的三行命令

- `Run ……` 选择安装一种网络解决方案，我们前面已经安装了

- `kubeadm join` 后续节点如何加入集群，包含了该节点的 IP 地址以及 token 等身份认证信息，请复制并使用自己电脑上生成的信息。我这里生成的信息为：

```bash
kubeadm join 172.16.1.11:6443 --token 80o5c0.wz84y38fx4zhgwsd \
    --discovery-token-ca-cert-hash sha256:5d0124a0ea9d3ccd0dc89670915dd6cf8acef73b87c95d6817981b6382f7d5e0
```

> 这里生成的 token 的有限期为 24小时，超时后，加入集群，需要手动生成新的 token 和 hash ，集体可以参考 [Joining your nodes](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#join-nodes)


> 安装过程中出现意外错误，可以参考 [Troublesshooting kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/) 获取帮助。

## 3.2 配置节点信息

此处建议使用拥有 sudo 权限的 `非 root`  账户，执行上图中的三条命令：

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## 3.3 安装依赖

在 kubeadm 当前 (v1.16.1) 版本的 Master 节点中，默认使用 CoreDNS 作为 DNS 服务器，需要手动选择并安装一种 [pod 网络解决方案](https://kubernetes.io/docs/concepts/cluster-administration/addons/)，否则 CoreDNS 将一直处于 `Pending` 状态：

![mark](https://cdn.sguan.top/markdown/20191007/nWYcSNK9wwhU.png?imageslim)

本文选择的方案是 [flannel](https://github.com/coreos/flannel)，执行命令即可完成安装：

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```


## 3.4 查看安装结果

如果一切顺利，我们的 Master 节点上的服务此时应该都已经完成安装，可以执行 `kubectl get node` 和 `kubectl  get pods  --all-namespaces` 命令查看。确认每个 pod 都已正常运行。

![mark](https://cdn.sguan.top/markdown/20191007/Qv3iCIvC5F7S.png?imageslim)

# 4 节点加入集群

该节点也需要按照 `第二章` 的内容完成基本配置。

然后粘贴并运行前面生成的命令：

```bash
kubeadm join 172.16.1.11:6443 --token 6gmreh.g2ofblu8bjytm310 \
    --discovery-token-ca-cert-hash sha256:764076a70b722ea7458558b47aee3799200506c8b440b6945375cb042c0eb4a0
```

通过 scp 复制前面创建的 Master 节点中的配置信息：

```bash
scp root@172.16.1.11:/etc/kubernetes/admin.conf /etc/kubernetes/admin.conf
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
kubectl get nodes
```

验证：

![mark](https://cdn.sguan.top/markdown/20191007/qBl6M63MjBYd.png?imageslim)

可以看到此时新节点已经加入集群，另外一个 Node 节点使用相同的方式加入集群。

kubectl delete deploy/pod xxx

```bash
# todo  加入集群成功了吗？角色正常吗？下一步~
```

##### 参考链接

[How to Install Kubernetes (k8s) 1.7 on CentOS 7 / RHEL 7](https://www.linuxtechi.com/install-kubernetes-1-7-centos7-rhel7/)