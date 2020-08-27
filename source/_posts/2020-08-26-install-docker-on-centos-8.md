---
title: 在 Centos 8 上安装 Docker
thumbnail: 'https://cdn.sguan.top/markdown/20200827133150.png'
disqusId: c4c7fb84-b18e-4315-bad3-4d8c2a1edaee
categories:
  - docker
tags:
  - docker
  - docker-compose
  - centos8
date: 2020-08-26 19:35:55
---

一个安装 Docker 的笔记

<!-- more -->

## 手动安装 containerd.io

```bash
Problem: package docker-ce-3:19.03.4-3.el7.x86_64 requires containerd.io >= 1.2.2-3
```

如果你收到类似上面这种提示，则需要手动安装 containerd.io

目前 docker 版本已经来到 19+，其对组件 containerd.io 的要求是 >= 1.2.2-3，由于 Centos 软件源更新不及时（后续版本更新后这个问题可能就不存在了），默认源下没有符合要求的版本，所以该组件无法完成安装，这里我们需要手动安装 containerd.io

### 安装

通过阿里云镜像安装：

```bash
dnf install https://mirrors.aliyun.com/docker-ce/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
```

如果你的服务器在国外，或者网络足够好的化，也可以直接从 docker 官方下载安装：

```bash
dnf install https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
```

此外，上面的命令中的 rpm 包可能随着时间的推移而变得陈久，但你可以直接访问 [Docker 官方](https://download.docker.com/linux/centos/7/x86_64/stable/Packages) 或者 [阿里云镜像](https://mirrors.aliyun.com/docker-ce/linux/centos/7/x86_64/stable/Packages) 查看并选择最新的 rpm 包。

## 安装 Docker

### 添加仓库

添加 Docker 官方的仓库信息：

```bash
$ sudo yum install -y yum-utils
$ sudo yum-config-manager  --add-repo  https://download.docker.com/linux/centos/docker-ce.repo
```

### 安装

正常的 Docker 安装： 

```bash
$ sudo yum install docker-ce docker-ce-cli
```

### 启动并添加开机启动项

```bash
$ sudo systemctl enable --now docker
```

### 验证

```bash
$ sudo docker run hello-world
```

等待镜像启动完成后，如果你可以看到的内容类似于下面这样，则表示安装成功：

```bash
$ sudo docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## 镜像加速

由于网络方面的原因，我们在拉取镜像的时候速度可能会很慢，对此，我们可以通过编辑配置文件，配置镜像来加快其速度。

在 Linux 系统中，默认情况下，Docker 的配置文件是 `/etc/docker/daemon.json`（[Configure the Docker daemon](https://docs.docker.com/config/daemon/#configure-the-docker-daemon)）

{% codeblock "/etc/docker/daemon.json" lang:json %}
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
{% endcodeblock %}

配置好镜像后，你需要执行以下命令：

```bash
$ systemctl daemon-reload 
$ systemctl restart docker
```

## 安装 Docker Compose

```bash
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

随后，你可以运行下面的命令查看 Docker Compose 版本，并以此判断是否安装成功：

```bash
$ docker-compose -v
docker-compose version 1.26.2, build eefe0d31
```

Docker Compose 可用于单机简单的编排，在学习、测试时是一个不错的选择。对于更加复杂的容器编排，唯一的选择是 [Kubernetes](https://kubernetes.io)

## 卸载

### 卸载 Docker Compose

```bash
sudo rm /usr/local/bin/docker-compose
```

### 卸载 Docker

```bash
$ sudo yum remove docker-ce docker-ce-cli containerd.io
```

但是，相关的镜像、容器、卷以及其它自定义目录下的内容不会被自动删除，你需要自己手动删除它们：

```bash
$ sudo rm -rf /var/lib/docker
# 删除其它自定义目录
$ sudo rm -rf ...
```

## 参考链接

[Install Docker Engine on CentOS](https://docs.docker.com/engine/install/centos/)

[CentOS之——CentOS8 安装 Docker](https://blog.csdn.net/l1028386804/article/details/105480007)

[Install Docker Compose](https://docs.docker.com/compose/install/#install-compose-on-linux-systems)