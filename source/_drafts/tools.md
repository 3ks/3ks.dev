---
title: "CentOS 下一些常用工具的安装"
date: 2020-02-22T16:47:24+08:00
tags: ["CentOS","tools"]
draft: true 

---

# 背景

在完成IP配置，并确保可以正常上网后，我们可以安装一些常用的工具

# 工具

### wget

> 可以将`wget`看作一个下载器

`yum install -y wget  `

### vim 

> 可能是神器，也可能只是一个文本编辑器,需要安装三部分

```bash
yum  install -y  vim-enhanced
yum  install -y  vim-minimal
yum  install -y  vim-common
```

### ifconfig

> 在确保网卡正常获取到 IP 地址，可以上网之后，发现 ifconfig 提示 ` ifconfig command not found` 这可能是因为没有安装相关的软件 `net-tools`

` yum install -y net-tools `

### sshd

> 这个没有安装的可能性很低，云服务器如果没有安装sshd，用户都没有办法连接，
> 但是本地安装 CentOS 可能出现没有安装的情况。

`yum install sshd-server`

### 安装 Git

`yum install -y git `

### 安装 Python 

` yum install -y python `


### 安装Pip

```bash
# 添加软件源，即可使用yum安装pip
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum install python-pip
pip install --upgrade pip
```