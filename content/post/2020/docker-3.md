---
title: "Docker 的 GitLab CI&CD"
date: 2020-02-22T17:24:26+08:00
tags: ["Docker","GitLab","CI","CD"]
draft: true 

---

# 基础环境

基于 Docker，我们希望搭建一个本地的 Gitlab 环境，Gitlab 需要依赖 `Redis`和`Postgresql`,所以一个基础的GitLab 需要安装三个容器，这里我们考虑使用 `Docker-compose`来实现这一套环境。
 
# 安装Gitlab及Runner

参考：docker-compose.yml

```yaml
version : "2"
services :

  gitlab-runner :
    image : gitlab/gitlab-runner:latest
    container_name : gitlab-runner
    volumes :
      - /var/run/docker.sock:/var/run/docker.sock
      - /containers/gitlab/gitlab-runner/config:/etc/gitlab-runner
      - /etc/docker:/etc/docker
      - /vendor:/vendor
    depends_on :
      - "gitlab-ce"

  gitlab-ce:
    image : gitlab/gitlab-ce:latest
    hostname : 192.168.8.8
    container_name : gitlab-ce
    mem_limit : 2300m
    environment :
      - GITLAB_PORT=80
      - GITLAB_SSH_PORT=20
      - GITLAB_SECRETS_DB_KEY_BASE=long-and-random-alpha-numeric-string
      - CONTAINER_TIMEZONE=Asia/Shanghai
    ports :
      - "20:20"
      - "80:80"
    volumes :
      - /containers/gitlab/gitlab-ce/config:/etc/gitlab
      - /containers/gitlab/gitlab-ce/logs:/var/log/gitlab
      - /containers/gitlab/gitlab-ce/data:/var/opt/gitlab
    depends_on :
      - "gitlab-redis"
      - "gitlab-postgresql"

  gitlab-redis :
    image : sameersbn/redis:latest
    container_name : gitlab-redis
    volumes :
      - /containers/gitlab/gitlab-redis:/var/lib/redis

  gitlab-postgresql :
    image : sameersbn/postgresql:latest
    container_name : gitlab-postgresql
    environment :
      - DB_NAME=gitlabhq_producton
      - DB_USER=gitlab
      - DB_PASS=password
    volumes :
      - /containers/gitlab/gitlab-postgresql:/var/lib/postgresql

```

### 启动服务

- 在服务器中创建配置文件`docker-compose.yml`,复制上面的内容，并根据自己的情况修改参数
- 执行命令 `docker-compose up`
- 正常情况下，我们Gitlab 服务会成功启动

![服务启动](http://cdn.sguan.top/markdown/20190111/nfdBdi9DgSvJ.png?imageslim)

- 访问服务器`IP:8080`可以看到Gitlab的界面，则服务正常启动了，这可能需要等待几分钟的时间。
 
![Gitlab界面](http://cdn.sguan.top/markdown/20190111/NYcnHQuNWAVE.png?imageslim)

- 第一次访问会强制要求修改密码，默认用户名为root，我这里将密码设为了`12345678`

一个简单的本地 Gitlab 环境就完成了搭建,我们这里使用的是社区版 11.6.3

![mark](http://cdn.sguan.top/markdown/20190118/nCsmJ6EqDeFK.png?imageslim)

# Gitlab CI 

### 创建 Gitlab 项目

要实现项目 CI/CD 等，我们需要先在 Gitlab 上有一个项目，可以新建，导入等多种方式，这里不详细讲解。

![mark](http://cdn.sguan.top/markdown/20190118/MwngycAmuSMY.png?imageslim)

### Gitlab Runner

> 基于Gitlab CI 我们想实现 Golang 的 CI/CD 即持续集成/持续部署, Gitlab 有一个 `Runner` 的概念，可以简单的将Runner理解为一个将 Gitlab 上的代码 pull 到 Runner 中进行测试，编译，生成镜像，并上传到镜像仓库等一系列功能的角色。

- 获取 Gitlab 地址及 Token

参考下图的顺序，依次展开 `Settings`->`CI/CD`->`Runners`->`Expand` 

![mark](http://cdn.sguan.top/markdown/20190118/SNUELsNjc89R.png?imageslim)

- Runner分为4种，这里我们注册的是 `Specific Runner` （其它的我暂时也不懂）。
 
- 在这里可以看到 Gitlab 地址以及 Token,在注册 Runner 的时候会用到。

![mark](http://cdn.sguan.top/markdown/20190118/jC3xOqeEwBtb.png?imageslim)

- 注册 Runner 

前面部署 Gitlab 的 Docker-compose.yml  已经包含了 Runner 容器，这里我们可以直接运行容器的的命令，在 Gitlab 中注册这个 Runner ,可以简单的理解为，Runner 向 Gitlab 发起请求，声明自己是 Runner 及一些基本信息 ,这个过程中 Gitlab 会通过 Token 验证身份。

```bash
docker exec -it gitlab-runner gitlab-ci-multi-runner register

# 然后按照提示输入相关信息, Gitlab 地址，Token (在上一步中获取)
# Runner名称，描述，Tag(这里有个坑)，运行环境，默认镜像（如果运行环境是Docker）等
```

![mark](http://cdn.sguan.top/markdown/20190118/bz6hbQkrVqSD.png?imageslim)

 `Tag`  不能乱填，可以暂时不填，后续详细说明
 
 `Executor` 这里填 `Docker`，因为我们想要让 Runner 将项目打包成一个 Docker镜像，所以需要运行 Docker
 
 `Default Docker Image` 这里填 `docker:dind` ，`dind`是`docker in docker`的意思,就是在 Docker 中运行 Docker 的意思，我们现在的环境就是这种情况。

然后可以看到提示注册成功，刷新 Gitlab 的网页界面，可以看到 Runner 出现了，另一个 Runner 是我之前注册的，不用管。

![mark](http://cdn.sguan.top/markdown/20190118/V4gnMVuBwGdM.png?imageslim)

> 一个坑，防火墙导致

![mark](http://cdn.sguan.top/markdown/20190111/nbc0Te0W0Vse.png?imageslim)

runner 无法注册，提示`no route to host`因为防火墙的原因，暂时关闭防火墙和 Selinux 即可，具体开放哪个端口有待研究

```bash
systemctl stop firewalld 
setenforce 0
```

- 修改 Runner 配置文件

`vim /containers/gitlab/gitlab-runner/config/config.toml`

找到我们刚刚注册的 Runner-1,默认内容大概如下，url，token会由于你的环境有所不同。

```toml
[[runners]]
  name = "Runner-1"
  url = "http://192.168.35.54:8080/"
  token = "Gf4979nszyPXCu4goeFz"
  executor = "docker"
  [runners.docker]
    tls_verify = false
    image = "docker:dind"
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
```

- 修改配置文件，可以参考下面的文件

```toml
[[runners]]
  name = "Runner-1"
  url = "http://192.168.35.54:8080/"
  token = "Gf4979nszyPXCu4goeFz"
  executor = "docker"
  [runners.docker]
    tls_verify = false
    image = "docker:dind"
    #特权模式，修改为 true
    privileged = true  
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    #映射docker.sock到容器内
    volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
    #将 Runner 连接到 gitlab 的网络
    network_mode = "gitlab_default"
    #
    shm_size = 0
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    
```

# Cloud not resolve host :xxx

编辑 `config.toml` 文件
```toml
[[runners]]
  ...
  [runners.docker]
    ...
    network_mode = "gitlab_default"
```

# DinD - Cannot connect to the Docker daemon.

在dind中，提示`DinD - Cannot connect to the Docker daemon. Is the docker daemon running on this host?`的错误，可能是因为没有映射宿主机的`/var/ryn/docker.sock` (docker-compose.yml似乎只能映射文件夹)

```toml
  [[runners]]
    name = "LocalRunner"
    url = "https://gitlab.com/ci"
    token = "**"
    executor = "docker"
    [runners.docker]
      tls_verify = false
      image = "docker:latest"
      privileged = true  #特权模式
      disable_cache = false
      cache_dir = "cache"
      volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
      #映射docker.sock 文件
    [runners.cache]
      Insecure = false

```

至此，Runner的准备工作就结束了，接下来就是编写 `.gitlab-ci.yml`,`Dockerfile`,`docker-compose.yml`等文件，让 Runner 按照我们给出的步骤执行操作了。


# Builder

Dockerfile

```Dockerfile
FROM golang:latest

RUN go get -u github.com/golang/dep/cmd/dep

```

make.sh

```bash
#!/bin/sh

docker build -t builder:latest .
```


# Runner

Dockerfile

```Dockerfile
FROM alpine:latest

#有C环境
#FROM frolvlad/alpine-glibc

RUN apk add --no-cache ca-certificates tzdata mysql-client

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime
```

make.sh

```bash
#!/bin/sh

docker build -t runner:2 .
```

> 参考链接

[利用docker搭建gitlab](https://blog.csdn.net/qq_14853889/article/details/79725726)

[Docker Compose 配置文件详解](https://www.jianshu.com/p/2217cfed29d7)

[DinD - Cannot connect to the Docker daemon. Is the docker daemon running on this host?](https://gitlab.com/gitlab-org/gitlab-runner/issues/1986)

[Gitlab runner docker Could not resolve host](https://stackoverflow.com/questions/50325932/gitlab-runner-docker-could-not-resolve-host)

[基于 GitLab 的 CI 实践](https://studygolang.com/articles/13996)

[Golang基于Gitlab CI/CD部署方案](https://yq.aliyun.com/articles/638794/#)

[Docker搭建GitLab-runner及关于golang的gitlab-ci配置](https://www.imooc.com/article/45813)

[通过 docker 搭建自用的 gitlab 服务](https://juejin.im/post/5a4c9ff36fb9a04507700fcc)

[通过docker-compose修改时区](https://www.dplor.com/node/333)
