---
title: "SSH 免密登陆"
date: 2020-02-22T16:52:25+08:00
tags: ["ssh","免密"]
draft: false 

---

# SSH 免密登录

1. 在服务器上生成用于登录的公钥和私钥

```bash
ssh-keygen -t rsa
```

首先提示输入生成文件的输出路径，然后是密码，最后是确认密码

> 密码是用于加密和登录时输入的密码，额，建议为空，不然还叫免密登录吗，但是请确保生成证书的安全，证书泄露可能很危险

> 生产环境强烈建议设置密码，毕竟稳定压倒一切,相当于双重验证吧

2. 导入公钥

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

3. 配置文件、文件夹的权限

```bash
#如果要开启严格模式，这一步需要正确配置权限
#如果不开启严格模式，可以跳过
sudo chmod -R 0700  ~/.ssh
sudo chmod -R 0600  ~/.ssh/authorized_keys
sudo chown -R guan:guan /home/guan
```

4.如果启用了SELinux，还需要执行（root用户把/home改成/root）

```bash
restorecon -R -v /home
```

5.修改SSH配置文件，支持使用证书登录（root权限）

```bash
vim /etc/ssh/sshd_config

# 可以去掉注释，也可以直接写在文件末尾？

RSAAuthentication yes  #RSA验证方式
StrictModes yes   #严格模式，会检查相关目录的权限,根据自己情况选择
PubkeyAuthentication yes  #允许用户使用自己生成的公私钥
AuthorizedKeysFile .ssh/authorized_keys  #公钥地址
PasswordAuthentication no #是否允许账号密码登录,根据自己情况选择
```

重启 SSH 服务

`systemctl restart sshd`

6. 客户端获取私钥

可以直接打开私钥文件然后复制内容到客户端（比如windows）

7. 使用PuTTY Key Generator 生成适用于 Putty 的证书

![mark](http://cdn.sguan.top/markdown/20181121/k1dWVLBE7vkr.png?imageslim)

选中私钥文件，然后点击保存为私钥 `Save private key`

8. PuTTY 使用证书实现免密登录

 - 打开Putty，加载或者新建一个会话
 
 - 点击 `connection`->`data` , 输入 `Auto-login username` (自动登录的用户名)
 
 - `connection`->`ssh`->`auth` 在`Private key file for authentication`选择私钥文件
 
 - `保存会话`，然后直接点击会话即可免密登录
 
![mark](http://cdn.sguan.top/markdown/20181121/dzLoRV3Uzaao.png?imageslim)

> 服务器存公钥，客户端存私钥

# 多个服务器共用一个公钥

> 首先一个巨坑，如果直接复制粘贴公钥至另一台服务器，很可能出错，导致服务器拒绝登录

正确的姿势之一是使用有公钥的服务器ssh登录并将公钥传输至另一个服务器，可以避免上面的问题

```bash
ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub
```

感谢阮一峰大佬的教程，网上的水文找了一大堆，都没有说到点子上。

# 参考链接

[CentOS 7 SSH使用证书登录](https://my.oschina.net/liting/blog/600098)

[Putty server refused our key的三种原因和解决方法](https://blog.51cto.com/callmepeanut/1336864)

[SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
