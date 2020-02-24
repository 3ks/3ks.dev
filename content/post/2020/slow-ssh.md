---
title: "解决 ssh 登陆慢的问题"
date: 2020-02-22T16:50:37+08:00
tags: ["ssh","slow"]
draft: false 

---

# 背景

OpenSSH 在用户登录的时候会验证IP，它根据用户的IP使用反向DNS找到主机名，再使用DNS找到IP地址，最后匹配一下登录的IP是否合法。如果客户机的IP没有域名，或者DNS服务器很慢或不通，会一直等到DNS超时，就会很花时间。（这种情况一般发生在本地没有域名的服务器）

> 修改 ssh 的配置文件，关闭 DNS 和 GSSAPI 验证即可加快登陆速度

编辑 OpenSSH 的主配置文件

- vi /etc/ssh/sshd_config

```bash
Port=22  #设置SSH的端口号是22(默认端口号为22)
Protocol 2  #启用SSH版本2协议
ListenAddress 192.168.0.222  #设置服务监听的地址
DenyUsers   user1 user2 foo  #拒绝访问的用户(用空格隔开)
AllowUsers  root osmond vivek  #允许访问的用户(用空格隔开)
PermitRootLogin  no  #禁止root用户登陆
PermitEmptyPasswords no  #用户登陆需要密码认证
PasswordAuthentication  yes  #启用口令认证方式
```

- vim /etc/ssh/sshd_config
 
修改 79 行左右的 `GSSAPIAuthentication` 为 `no`

![mark](http://cdn.sguan.top/markdown/20181121/qUFk9LenHzFh.png?imageslim)

修改 115 行左右的 `UseDNS` 为 `no`

![mark](http://cdn.sguan.top/markdown/20181121/44XyVT15gM0Q.png?imageslim)

- systemctl restart sshd

> 重新登录测试