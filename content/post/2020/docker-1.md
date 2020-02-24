---
title: "CentOS7 安装 Docker"
date: 2020-02-22T17:16:30+08:00
tags: ["Docker","install"]
draft: false 

---

# CentOS7 安装 Docker

### 使用 yum 安装

> 安装一些必要的系统工具
>
```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

> 添加软件源信息

```bash
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

#如果是国外的服务器，直接添加docker官方软件源

sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

> 更新 yum 缓存

```bash
sudo yum makecache fast
```

> 安装 Docker-ce

```bash
sudo yum -y install docker-ce
```

> 启动 Docker 后台服务

```bash
sudo systemctl start docker
```

> 开机启动 Docker 后台服务

```bash
sudo systemctl enable docker
```

> 将非root用户添加至docker组

```bash
非root用户在使用docker时，可能需要经常sudo，
将该用户添加至docker用户组可以解决这个麻烦
执行完后重启docker，重新登陆ssh即可生效

sudo usermod -aG docker username
```


> 测试运行 hello-world

```bash
docker run hello-world
```

下图表示 Docker 已成功安装并运行

![mark](http://cdn.sguan.top/markdown/20181121/G02nYdGVa70I.png?imageslim)


### 镜像加速

> 如果由于没有掌握科学上网方式等原因导致拉取 Docker 镜像十分缓慢，可以配置 Docker 镜像来加速

vim /etc/docker/daemon.json

- 官方镜像

```bash
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

- 163镜像

```bash
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

- 阿里云镜像

```bash
{
  "registry-mirrors": ["https://almtd3fa.mirror.aliyuncs.com"]
}
```

- 重新加载配置文件并重启 Docker 服务

```bash
systemctl daemon-reload
systemctl restart docker
```

### 删除 Docker CE

```bash
sudo yum remove docker-ce
sudo rm -rf /var/lib/docker
```

# Docker 无法启动或使用

> 安装完 docker 后使用出现下图的提示

![mark](http://cdn.sguan.top/markdown/20181126/f5HU3ewRRcLd.png?imageslim)

 大意就是权限不够，可以使用 `root 账号` 运行 docker 或者将当前用户加入到 `docker 用户组`即可。

> 将当前用户加入至 `docker 用户组`

```bash
sudo gpasswd -a $USER docker    #将当前用户添加至 docker用户组
newgrp docker     # 更新用户组
docker ps     # 测试
```

# 配置别名（快捷键）

- vim  `/etc/bashrc` 

内容如下，这取决于个人习惯

```bash
alias dk='docker';
alias dc='docker container';
alias db='docker build';
alias di='docker image';
alias ds='docker images';
alias dp='docker-compose';
```

- 保存退出后，`source /etc/bashrc` 使配置生效

# Docker-compose 安装

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose &&  chmod +x /usr/local/bin/docker-compose
```

# 参考链接

[CentOS Docker安装](http://www.runoob.com/docker/centos-docker-install.html)

[Docker-compose 安装](https://yeasy.gitbooks.io/docker_practice/compose/install.html)

[Docker Compose 配置文件详解](https://www.jianshu.com/p/2217cfed29d7)

[Dockerfile教程](https://blog.csdn.net/pushiqiang/article/details/78714628)

[Docker启动Get Permission Denied](https://www.cnblogs.com/informatics/p/8276172.html)
