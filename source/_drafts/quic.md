---
title: "简单 QUIC 教程"
date: 2020-02-22T10:58:08+08:00
tags: ["HTTP 3","QUIC","Caddy"]
draft: true 

---

# 前言

- emmm~,今天是9102年儿童节，都9102年了，你的网站还是万年HTTP吗，Chrome已经看你不爽很久了，得益于Let's Encrypt，现在申请TLS证书变得异常简单。

![mark](https://cdn.sguan.top/markdown/20190601/4FHJ3R47NG9d.png?imageslim)


- 本文将分享我是如何开启整站HTTPS和QUIC，网上关于QUIC的介绍文章挺多的，既然你在看这篇文章，相信是对QUIC有一定了解的，本文不在做重复介绍相关概念。
- 网上的很多文章教程略微复杂，本文将带领你从0开始部署，大学生都学得会。
- 本文使用的浏览器是Chrome73，协议是QUIC43。最终效果如下。

![mark](https://cdn.sguan.top/markdown/20190405/VVUpvJnSzyJN.png?imageslim)

# 前置条件

虽然说是从0开始，但是你还是得准备一些东西：

- 有一定的Linux基础，至少要能跟着做吧~
- 拥有或注册一个自己的域名。
- 拥有一台服务器，需要将域名解析到该服务器。
- 安装 `Docker&Docker-compose`环境，这会使整个过程变得非常简单，花几分钟安装Docker是值得的，如果不会的同学，请参考前面的教程，安装Docker。

# DNS解析

- 如果你知道如何配置DNS，可以跳过
- 主机记录@，记录类型A，记录值你的服务器公网IP地址。
- 主机记录*，记录类型CNAME，记录值你域名(末尾有个.)
- 大概就是下面这个样子。
![mark](https://cdn.sguan.top/markdown/20190405/lMV1emK21F1v.png?imageslim)

# 注意

- 这里我默认你已经安装好了`Docker`以及`Docker-compose`，即将会使用到该工具，如果没有正确安装，请看安装这两个工具的教程。
- 后续操作我是在CentOS7下面进行的，请确保你的账号有足够的权限(root)

# 准备配置文件

- mkdir -p /containers/caddy
- cd /containers/caddy
- vi docker-compose.yml

```yaml
version: '2'
services:
  caddy-server:
    image: abiosoft/caddy:latest
    container_name: caddy-server
    ports:
      - "443:443/udp"
      - "443:443"
      - "80:80"
    volumes:
      - "/containers/caddy/Caddyfile:/etc/Caddyfile"
      - "/containers/caddy/.caddy:/root/.caddy"
      - "/containers/caddy/log:/root/log"
      - "/containers/caddy/www:/www"
    restart: always
    entrypoint :  "tail -f /dev/null"
```

- 保存退出

- vi ./www/index.html

```html
<h1>Hello World</h1>
```
- 保存退出

- vi Caddyfile

```Caddyfile
# 请替换为自己的域名
https://www.1996.live {
gzip
# 请替换为自己的邮箱
tls imqksl@gmail.com
root /www
log /root/log/log.log
errors /root/log/error.log 
}
```

- 保存退出
- docker-compose up -d
- docker ps

> 如果没有意外，你这个时候应该是跟我的截图类似，注意 STATUS   Up * Seconds应该就是正常启动了。

![mark](https://cdn.sguan.top/markdown/20190405/YG5WnKC8KSIj.png?imageslim)

- docker exec -i -t caddy-server sh
> 执行这条命令后，我们会进入该容器内容，可以通过命令行左侧路径分辨出来。

- caddy -quic -conf /etc/Caddyfile
> 执行这条命令后，Caddy会询问是否同意相关协议。怎么选，同学你自己心里有数吧。

![mark](https://cdn.sguan.top/markdown/20190405/pCCiQdiQ2m6S.png?imageslim)

> 然后Caddy会自动去申请HTTPS证书，免费的，一般两分钟以内完成。 这里可能会让你再输入一次邮箱地址。完成后，大概就是这个样子。

![mark](https://cdn.sguan.top/markdown/20190405/y4fAuylGOUny.png?imageslim)

> 通过域名访问即可，注意使用https://

> 请关闭科学上网，代理工具等，因为他可能会将UDP数据代理至TCP，而QUIC基于UDP，就会导致QUIC连接建立失败，此时连接使用HTTP/2 以及TLS1.3证书。

> 请确保服务器防火墙允许80，443端口流量通过

![mark](https://cdn.sguan.top/markdown/20190405/S5YKdem7grcr.png?imageslim)

# 善后处理

- ctrl+c
- exit
- docker-compose down
- vi docker-compose.yml

```yaml
version: '2'
services:
  caddy-server:
    image: abiosoft/caddy:latest
    container_name: caddy-server
    ports:
      - "443:443/udp"
      - "443:443"
      - "80:80"
    volumes:
      - "/containers/caddy/Caddyfile:/etc/Caddyfile"
      - "/containers/caddy/.caddy:/root/.caddy"
      - "/containers/caddy/log:/root/log"
      - "/containers/caddy/www:/www"
    restart: always
    entrypoint :  "caddy -quic -conf /etc/Caddyfile"  # 整个文件只修改这里
```

> 这个文件只修改了最后一行的内容

- docker-compose up -d
- systemctl enable docker

> 善后的步骤挺多余的，由于初次运行Caddy需要同意用户协议，作者不知道该怎么让docker-compose的容器接受键盘输入，出此下策，希望有懂的朋友指点一下。


> 自此，如果没有意外的话，以后该服务器都会自动运行。


> 静态文件根据自己的需求替换即可

> Caddy也有反向代理等功能，可以轻松实现整站QUIC，后面会讲到。

> 如果你已经完全安装本文操作，但是依然不能建立QUIC连接，一个可能的原因是ISP阻断了UDP数据，可以让其它地区的朋友帮忙测试一下。

# 强制HTTPS

完成前面的操作后，虽然我们可以通过输入`https://yourdomain`实现HTTPS连接，但是如果输入`http://yourdomain`浏览器与服务器的连接还是HTTP连接。这里我们通过配置Caddyfile的内容就可以实现强制HTTPS，原理很简单，将HTTP重定向至HTTPS即可。

- vi Caddyfile

```Caddyfile
http://www.1996.live  http://1996.live  https://1996.live{
    redir https://www.1996.live{url}
}
# 请替换为自己的域名
https://www.1996.live {
gzip
# 请替换为自己的邮箱
tls imqksl@gmail.com
root /www
log /root/log/log.log
errors /root/log/error.log 
}
```

- docker-compose down
- docker-compose up -d

# 整站HTTPS

- 我们的网站可能不只一个Web服务，比如我除了`www.1996.live`的主页以外，在另一台服务器上还跑了一个`harbor`镜像仓库，域名已经指向当前服务器的IP地址，镜像仓库只能通过IP+端口访问。

![mark](https://cdn.sguan.top/markdown/20190601/SLOXoAqQmOc8.png?imageslim)


- 通过反向代理功能，可以轻松的将域名指向服务器上的另一个或者另一个服务器上的应用，Apache，Caddy，Nginx，同时因为是通过Caddy访问的，连接依然是HTTPS和QUIC的，（Caddy与应用之间应该是普通的HTTP）。
- 比如这里我打算给我的镜像仓库分配域名`hub.1996.live`，修改Caddyfile即可。
- vi Caddyfile

```
http://www.1996.live  http://1996.live  https://1996.live{
    redir https://www.1996.live{url}
}
# 请替换为自己的域名
https://www.1996.live {
gzip
# 请替换为自己的邮箱
tls imqksl@gmail.com
root /www
log /root/log/log.log
errors /root/log/error.log 
}

# 添加下面的这部分内容即可
http://hub.1996.live{
    redir https://hub.1996.live{url}
}
https://hub.1996.live {
gzip
tls imqksl@gmail.com
# 填写另一个应用所在服务器的IP地址和端口即可
# 一般填局域网IP地址，速度更快，更安全。
proxy / http://157.230.159.156 {
  header_upstream Host {host}
  header_upstream X-Real-IP {remote}
  header_upstream X-Forwarded-For {remote}
  header_upstream X-Forwarded-Proto {scheme}
}
log /root/log/log.log
errors /root/log/error.log 
}
```

- docker-compose down
- docker-compose up -d

![mark](https://cdn.sguan.top/markdown/20190405/a8KVjpvxeMTB.png?imageslim)

> ### 参考链接

[怎么把网站升级到QUIC以及QUIC特性分析](https://www.yinchengli.com/2018/06/10/quic/)

[本站开始支持 QUIC](https://halfrost.com/quic_start/#1)

[Caddy Web服务器QUIC部署](https://www.jianshu.com/p/15fc32760993)
