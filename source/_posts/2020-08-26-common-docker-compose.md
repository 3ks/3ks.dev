---
title: 个人常用的 docker-compose 配置文件
thumbnail: 'https://cdn.sguan.top/markdown/20200827140114.png'
disqusId: 27fbdebe-7eb5-4aa1-a2ce-2d6f021621f7
categories:
  - ["docker","docker-compose"]
tags:
  - docker
  - docker-compose
date: 2020-08-26 21:54:17
---

在平时做各种小实验时，通过 docker-compose 可以快速的搭建一个环境，记录一下自己用到的一些清单

<!-- more -->

## 习惯

- 根目录是 `/cs` （containers）
- 为每个项目创建一个目录，存放其相关的资源文件、docker-compose.yaml 等

## Mysql

{% codeblock "docker-compose.yaml" lang:yaml %}
version : '2'
services :
  mysql :
    image : mysql:latest
    container_name : mysql8
    ports :
      - "3306:3306"
    volumes :
      - "/cs/mysql/data:/var/lib/mysql"
    environment :
      MYSQL_ROOT_PASSWORD : password123
    restart : always
{% endcodeblock %}

## Redis

{% codeblock "docker-compose.yaml" lang:yaml %}
version : "2"
services :
  redis :
    image : redis:latest
    container_name : redis
    volumes :
      - /cs/redis/data:/data
    ports :
      - "6379:6379"
    restart : "always"
    entrypoint : redis-server --requirepass "password123"
{% endcodeblock %}

## FTP

{% codeblock "docker-compose.yaml" lang:yaml %}
version: '2'
services:
  ftpd_server:
    image: stilliard/pure-ftpd:hardened
    container_name: pure-ftpd
    ports:
      - "21:21"
      - "30000-30009:30000-30009"
    volumes:
      - "/images/ftp/data:/home/guan/"
      - "/images/ftp/passwd:/etc/pure-ftpd/passwd"
    environment:
      PUBLICHOST: "192.168.1.90"
      FTP_USER_NAME: guan
      FTP_USER_PASS: mypass
      FTP_USER_HOME: /home/guan
    restart: always
{% endcodeblock %}

如果连接卡在`读取目录列表`可以参考 [FileZilla 读取目录列表失败的解决办法 ftp](https://blog.csdn.net/zhangfeng1133/article/details/47418121)

## Nginx

{% codeblock "docker-compose.yaml" lang:yaml %}
version: '2'
services:
  nginx-server:
    image: nginx:latest
    container_name: nginx-server
    ports:
      - "80:80"
    volumes:
      - "/cs/nginx/html:/usr/share/nginx/html"
      - "/cs/nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "/cs/nginx/log:/var/log/nginx"
    restart: always
{% endcodeblock %}


你还需要准备静态文件，并将其放到 `/cs/nginx/html/` 下

你还需要准备 nginx 的配置文件，其绝对路径为 `/cs/ngix/nginx.conf`


这是一个 nginx.conf 的示例：

{% codeblock "nginx.conf" lang:conf %}
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
{% endcodeblock %}

## Postgresql

{% codeblock "docker-compose.yaml" lang:yaml %}
version : "2"
services :
  postgres :
    image : "postgres:latest"
    container_name : "postgres"
    ports :
      - "5432:5432"
    volumes :
      - "/cs/postgres/data:/var/lib/postgresql/data"
    environment :
      POSTGRES_PASSWORD : "mypassword"
{% endcodeblock %}

## GitLab

### 版本区别

GitLab 有两个版本，CE 和 EE。而 EE 又提供了多种级别的付费计划（包括一种免费的计划）。

免费的 GitLab EE 与 CE 完全相同，区别在于升级到付费计划时，GitLab EE 更方便。如果有付费的可能，则最好安装 GitLab EE。如果完全没有付费的打算，则安装 GitLab CE。

可以查看 [Community Edition or Enterprise Edition](https://about.gitlab.com/install/ce-or-ee/) 获取详情

### 配置文件 

{% codeblock "docker-compose.yaml" lang:yaml %}
web:
  image: 'gitlab/gitlab-ce:latest'
  restart: always
  hostname: 'web'
  environment:
    GITLAB_OMNIBUS_CONFIG: |
      external_url 'http://192.168.7.11:80'
  ports:
    - '8080:80'
  volumes:
    - '/cs/gitlab/config:/etc/gitlab'
    - '/cs/gitlab/logs:/var/log/gitlab'
    - '/cs/gitlab/data:/var/opt/gitlab'
{% endcodeblock %}

其中：

- `external_url` 是必填的，其端口决定了其监听端口，我们在 `ports` 映射端口时需要注意。其地址会影响 `clone` 等按钮生成的 URL，在映射端口不一致的情况下，需要注意一下。
- `GITLAB_OMNIBUS_CONFIG` 还支持其它参数，查看 [Pre-configure Docker container](https://docs.gitlab.com/omnibus/docker/#pre-configure-docker-container) 获取详情 

## Harbor

### 前提条件

Harbor 组件较多，官方提供了安装脚本，直接下载使用即可，默认情况下，有以下[要求](https://goharbor.io/docs/2.0.0/install-config/installation-prereqs/)：
 
- 至少双核 CPU、4G 内存、40 G 硬盘，建议 4 核 CPU、8G 内存、160 G 硬盘
- 已安装 Docker 17.06+、Docker Compose 1.18.0+ 以及 Openssl
- 可以使用端口：80、443、4443

## 参考连接

[mac或者linux通过docker管理nginx部署静态文件](https://www.jianshu.com/p/52cd108a5e89)

[nginx 403 forbidden 二种原因](http://blog.51yip.com/apachenginx/1512.html)

[postgres](https://hub.docker.com/_/postgres)

[postgresql数据库改变data目录](https://blog.csdn.net/u014373825/article/details/41477607)

[Docker上部署FTP服务器（基于stilliard/pure-ftpd）](https://blog.csdn.net/Aria_Miazzy/article/details/83686834)

[Install GitLab using Docker Compose](https://docs.gitlab.com/omnibus/docker/#install-gitlab-using-docker-compose)
