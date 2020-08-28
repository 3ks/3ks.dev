---
title: "Docker-Harbor"
date: 2020-02-22T17:29:04+08:00
tags: ["Docker","Harbor"]
draft: true 

---

# 申请证书

```bash
cd ~/.acme.sh/
sh acme.sh --issue -d oneblog.club --standalone
sh acme.sh  --installcert  -d  oneblog.club   \
        --key-file   /opt/certs/oneblog.club.key \
        --fullchain-file /opt/certs/fullchain.cer
```

# 私有镜像仓库

在前面 Gitlab CI/CD 中打包好的镜像，我们可以直接将镜像上传至 DockerHub 等公共镜像仓库，但是考虑到性能及安全方面的问题，我们尽量搭建私有的镜像仓库。

Harbor 就是一款简单易用的私有镜像仓库。


# 安装 Harbor

- 如果已经安装好 Docker,Docker-compose等环境，则可以直接开始下载并安装 Harbor 了

```bash
wget https://storage.googleapis.com/harbor-releases/release-1.9.0/harbor-offline-installer-v1.9.1.tgz 
tar -zxvf harbor-offline-installer-v1.9.1.tgz
```

- 修改目录下的配置文件 harbor.cfg

- 运行 install.sh 自动安装 Harbor

> 一个错误

[Step 2]: preparing environment ...
install.sh: ./prepare: /usr/bin/python: bad interpreter: No such file or directory

打扰了，下载新版Harbor即可

# 添加镜像仓库

```bash
vim /etc/docker/daemon.json 

"insecure-registries":["http://192.168.190.101:80"]


# 保存退出

systemctl daemon-reload
systemctl restart docker
```

目前私有仓库是http的，所以算是非安全的

# Error

> Pulling user (192.168.2.2:2080/oneblog/user:latest)...
ERROR: Get https://192.168.2.2:2080/v2/: http: server gave HTTP response to HTTPS client

由于docker拉取的目标仓库是非安全的镜像仓库，且不在信任列表内，需要将目标仓库域名加入信任列表

- vi /etc/docker/daemon.json

按照JSON数据格式，添加或者追加该仓库

```json
{
    "insecure-registries":["http://192.168.2.11:2080"]
}
```

# 反向代理Harbor 上传镜像出错`docker push unknown blob`

> 待解决，要解决！

> 参考连接

[Docker系列文章（一）：基于 Harbor 搭建 Docker 私有镜像仓库](http://www.54tianzhisheng.cn/2017/11/26/Docker-harbor/)

[docker从私有仓库Harbor， push 及 pull 镜像](https://my.oschina.net/u/1867229/blog/1542477)

[vmware Harbor 更改访问端口](https://blog.csdn.net/kozazyh/article/details/79829374)

[可能是最详细的部署:Docker Registry企业级私有镜像仓库Harbor管理WEB UI](https://juejin.im/post/5a52f858f265da3e4e2577fd#heading-16)

[自定义端口](https://github.com/goharbor/harbor/blob/master/docs/installation_guide.md#for-http-protocol)