---
title: "V2ray 的安装使用"
date: 2020-02-22T17:11:55+08:00
tags: ["v2ray","科学上网技术"]
draft: true 

---

# 1 下载地址

V2Ray 的程序既可以是客户端，又可以是服务端，根据自己的平台选择。

官方下载地址：[Github](https://github.com/v2ray/v2ray-core/releases)

官方 Docker 镜像： [DockerHub](https://hub.docker.com/r/v2ray/official/)

# 2 简单用法

### 2.1 服务端

> v2ray

config.json

```json
{
"inbounds": [
    {
      "port": 9001,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "3c0f4754-cf00-4434-9f5d-8cb745336306",
            "alterId": 64
          }
        ]
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```

> docker

docker-compose.yml

```yaml
version: "2"
services:
  v2ray-server:
    image: v2ray/official
    container_name: v2ray-server
    ports:
      - "9001:9001"
    volumes:
      - "/cs/v2ray/v2ray:/etc/v2ray"
    entrypoint : "v2ray -config=/etc/v2ray/config.json"
```

### 2.2 客户端

config.json

```json
{
  "inbounds": [
    {
      "port": 1070,
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth"
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "167.172.117.254",
            "port": 9001,
            "users": [
              {
                "id": "3c0f4754-cf00-4434-9f5d-8cb745336306",
                "alterId": 64
              }
            ]
          }
        ]
      },
	  "mux": {"enabled": true}
    }
  ]
}
```

# 3 WS + TLS + Web

### 3.1 服务端

> v2ray

config.json

```json
{
"inbounds": [
    {
      "port": 9001,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "3c0f4754-cf00-4434-9f5d-8cb745336306",
            "alterId": 64
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
        "path": "/ray/"
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```

> docker 

docker-compose.yml

```yaml
version: '2'
services:
  caddy-server:
    image: caddy/caddy:alpine
    container_name: caddy-server
    user: root
    ports:
      - "443:443/udp"
      - "443:443"
      - "80:80"
    volumes:
      - "/cs/caddy/Caddyfile:/etc/caddy/Caddyfile"
      - "/cs/caddy/caddy:/root/.local/share/caddy"
      - "/cs/caddy/log:/root/log"
      - "/cs/caddy/www:/www"
      - "/cs/caddy/admin:/admin"
    restart: always
    entrypoint :  "caddy run --config=/etc/caddy/Caddyfile"
  v2ray-server:
      image: v2ray/official
      container_name: v2ray-server
      volumes:
        - "/cs/caddy/v2ray:/etc/v2ray"
      entrypoint : "v2ray -config=/etc/v2ray/config.json"
```

> Caddy

Caddyfile

```
{
  email gorda@linux.com
  experimental_http3
}

tech.gorda.io:443 {
  reverse_proxy /ray/* v2ray-server:9001
  root * /www
  file_server
}

gorda.io:443 {
  redir  https://www.{host}{url}
}

www.gorda.io:443 {
  root * /www
  file_server
}
```

### 3.2 客户端

config.json

```json
{
  "inbounds": [
    {
      "port": 1070,
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth"
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "tech.gorda.io",
            "port": 443,
            "users": [
              {
                "id": "3c0f4754-cf00-4434-9f5d-8cb745336306",
                "alterId": 64
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "security": "tls",
        "wsSettings": {
          "path": "/ray/"
        }
      },
	  "mux": {"enabled": true}
    }
  ]
}
```

# 客户端的 inbounds

客户端的 inbouds 协议一般是 socks，chrome 默认不支持 chrome，对于新装的系统，还没登陆谷歌账号，安装扩展，此时可以将协议修改为 http，这样就可以通过 HTTP 代理了。

后续使用什么协议视自己情况选择。

config.json

```json
{
  "inbounds": [
    {
      "port": 1070,
      "protocol": "http",   // 仅一行
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth"
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "tech.gorda.io",
            "port": 443,
            "users": [
              {
                "id": "3c0f4754-cf00-4434-9f5d-8cb745336306",
                "alterId": 64
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "security": "tls",
        "wsSettings": {
          "path": "/ray/"
        }
      },
	  "mux": {"enabled": true}
    }
  ]
}
```

# 参考链接

- [新 V2Ray 白话文指南](https://guide.v2fly.org/)
- [Caddy Documention](https://caddyserver.com/docs/)
