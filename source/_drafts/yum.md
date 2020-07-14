---
title: "配置 yum 源"
date: 2020-02-22T16:45:34+08:00
tags: ["CentOS","yum"]
draft: false 

---

# 背景

由于总所周知的原因，国内网速较慢，访问国外资源可以选择 `科学上网` 或者 `国内镜像`

# 步骤

1. 备份当前 yum 源

`sudo cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak`

2. 设置阿里云 yum 源

`sudo wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo`

3. 添加EPEL源

`sudo wget -P /etc/yum.repos.d/ http://mirrors.aliyun.com/repo/epel-7.repo`

4. 清理缓存并生成新的缓存

```bash
sudo yum clean all  
sudo yum makecache
```

# 参考链接

[CentOS 7 使用阿里云的yum源](https://blog.csdn.net/skykingf/article/details/51953700)

[网易163-CentOS镜像使用帮助](https://mirrors.163.com/.help/centos.html)

[清华-CentOS 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/centos/)
