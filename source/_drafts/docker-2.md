---
title: "Docker 常用容器安装"
date: 2020-02-22T17:20:00+08:00
tags: ["Docker","base"]
draft: true 

---

# Mysql

```yaml
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
```

运行mysql5.7，如果本地没有镜像会自动拉取
-p	端口映射
数据库密码 shengdian
-d 	后台运行
-v	挂载宿主机的目录到容器内，实现数据持久化

# Redis

```yaml
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
```

# FTP

- docker-compose.yml

```yaml
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
```

- docker-compose up -d

- 连接

如果连接卡在`读取目录列表`可以参考 [FileZilla 读取目录列表失败的解决办法 ftp](https://blog.csdn.net/zhangfeng1133/article/details/47418121)

# Nginx

- docker-compose.yml
```yaml
version: '2'
services:
  nginx-server:
    image: nginx:latest
    container_name: nginx-server
    ports:
      - "80:80"
    volumes:
      - "/containers/nginx/html:/usr/share/nginx/html"
      - "/containers/nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "/containers/nginx/log:/var/log/nginx"
    restart: always
```

- vim nginx.conf

```conf
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

```

- nginx/html/index.html

```html
Hello World
```

- docker-compose up -d 
 
- 反向代理配置 

reverse-proxy.conf

```conf
server
{
    listen 443 ssl http2;
    server_name hub.oneblog.club;

    ssl_certificate          ./oneblog.club.crt;
    ssl_certificate_key      ./oneblog.club.key;

    ssl_session_timeout  5m;

    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers   on;


        location / {
                proxy_redirect off;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_pass https://165.227.53.109;
        }
        access_log /var/log/nginx/hub.oneblog.log;
  }

```

# Postgresql

- vi docker-compose.yml

```yaml
version : "2"
services :
  postgres :
    image : "postgres:latest"
    container_name : "postgres"
    ports :
      - "5432:5432"
    volumes :
      - "/containers/postgres/data:/var/lib/postgresql/data"
    environment :
      POSTGRES_PASSWORD : "mypassword"
```

# 参考连接

[mac或者linux通过docker管理nginx部署静态文件](https://www.jianshu.com/p/52cd108a5e89)

[nginx 403 forbidden 二种原因](http://blog.51yip.com/apachenginx/1512.html)

[postgres](https://hub.docker.com/_/postgres)

[postgresql数据库改变data目录](https://blog.csdn.net/u014373825/article/details/41477607)

[Docker上部署FTP服务器（基于stilliard/pure-ftpd）](https://blog.csdn.net/Aria_Miazzy/article/details/83686834)
