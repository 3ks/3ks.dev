---
title: SSH 配置文件
thumbnail: 'https://cdn.sguan.top/markdown/20200901233157.png'
disqusId: ddfb2799-038a-41e3-8a8e-f69fb7895ee1
categories:
  - ssh
tags:
  - ssh
date: 2020-09-01 23:30:03
---

之前说到了通过证书进行 SSH 免密登录，但在管理多个私钥的时候又有了新的问题。

<!-- more -->

## 多个密钥对

在只有一份密钥对的时候，将公钥放在服务器上，并配置好 SSH，客户端将私钥保存为 `~/.ssh/id_rsa` 即可进行免密登录，这个过程叫做 `公钥登录`。例如：

```bash
$ ssh root@192.168.1.1
```

但是，当拥有多个密钥对时，如何使用呢？

实际上，我们可以通过 `-i` 参数，指定要使用的私钥，这样就可以使用多个私钥了。例如：

```bash
$ ssh  -i ~/.ssh/id_local root@192.168.1.1
```

-i：identity_file，该参数指定要使用的证书。

之所以前面我们不指定该参数，也可以进行免密登录，是因为 ssh 默认会尝试并使用 `~/.ssh` 目录下的几个证书（id_ecdsa、id_ed25519、id_rsa 等），如果存在且可用的话则使用，否则会提示输入密码。

## config

虽然我们可以通过 -i 指定私钥，但随着数量增多，还是存在一些不便。此时我们可以通过配置 `config` 文件，来为不同主机配置不同的私钥，而无需每次都指定。

此外，还可以为主机配置别名，免去记不住 IP 地址的烦恼：

{% codeblock "~/.ssh/config" lang:config %}
Host host1
HostName 192.168.7.11
User root
Port 22
IdentityFile ~/.ssh/id_rsa

Host host2
HostName 192.168.7.12
User root
Port 22
{% endcodeblock %}

`Host` 相当于别名，可以通过该值替代 `HostName`

`IdentityFile` 不是必须的，不指定则相当于常规方式登录。

## 参考链接

[使用 SSH config 文件](https://daemon369.github.io/ssh/2015/03/21/using-ssh-config-file)