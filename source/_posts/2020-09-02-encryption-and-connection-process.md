---
title: SSH 加密及连接过程
thumbnail: 'https://cdn.sguan.top/markdown/20200902135106.png'
disqusId: 6f6c9a9a-1f44-4d6b-9126-3b422e5c113a
categories:
  - ssh
tags:
  - ssh
  - encryption
date: 2020-09-02 13:44:56
---

公钥登录是连接服务器的一种常见操作，在此记录一下我的看法

<!-- more -->

## 概述

SSH 登录过程我认为应该分为两步：连接与验证，很多文章在介绍原理时，忽略了连接步骤，只讲了验证这一步，不知道这是否有必要，总是我还是写写吧，加深理解。

## 连接

SSH，全称 Secure Shell，是一种加密的网络传输协议，可在不安全网络中为网络服务提供安全的传输环境。

安装有 SSH 的服务端，会有一份默认的密钥对，当有客户端尝试进行 SSH 登录时，服务端会将公钥下发给客户端。对于首次连接的服务端，会提示一个我们经常看到的内容：

```bash
$ ssh root@192.168.7.11
The authenticity of host '192.168.7.11 (192.168.7.11)' can't be established.
ECDSA key fingerprint is SHA256:qjwNOQ46uFsG6kDScW1JfkVzt6ms4FMPFPNRkMtNoLY.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

大意是：这个未知的主机不一定可信，它的指纹是 `SHA256:qjwNOQ46uFsG6kDScW1JfkVzt6ms4FMPFPNRkMtNoLY`，你想要继续建立连接吗？此时我们一般会输入 `yes` 继续连接。

随后，并不是客户端使用公钥将密码加密码传输给服务端进行验证，而是需要先连接，大致步骤如下：

1. 通信双方协商一个 `质数1` 作为种子，一种`加密方式`后续会用到
1. 通信双方再各自生成一个 `质数`，该质数作为对方的`私钥`，可以叫做 `Server-私钥` 和 `Client-私钥`，不告知对方。
1. 服务端使用 `Server-私钥`、`质数1`根据`加密方式`生成`Server-公钥`；客户端类似的，生成`Client-公钥`
1. 通信双方交换公钥。
1. 服务端使用 `Server-私钥`、`Client-公钥`根据`加密方式`生成`通信密钥`，客户端使用 `Client-私钥`、`Server-公钥`根据`加密方式`生成`通信密钥`。此时 `通信密钥1` 必然与 `通信密钥2` 完全相同，相关证明可以查看[迪菲-赫尔曼密钥交换](https://zh.wikipedia.org/wiki/%E8%BF%AA%E8%8F%B2-%E8%B5%AB%E7%88%BE%E6%9B%BC%E5%AF%86%E9%91%B0%E4%BA%A4%E6%8F%9B)
1. 使用`通信密钥`进行加密通信。

## 验证

`口令登录`和`公钥登录`只是两种不同的身份验证方式，它们都需要建立在加密连接的基础之上，传输的数据均通过`通信密钥`加密。能不能连接是一码事，能不能通过身份验证又是另一码事。

### 口令登录

此时的过程大致如下：

1. 客户端通过 `公钥加密(密码)` 将密码加密传输给服务端
1. 服务端通过 `私钥解密(密码)` 获取到密码
1. 密码验证通过则允许登录

## 公钥登录

此时的过程大致如下：

1. 客户端将`密钥对ID`发送给服务端
1. 服务端检查是否有匹配`密钥对ID`的公钥（这些公钥一般保存在 `~/.ssh/authorized_keys` 下）
1. 如果找到，则使用该公钥对`随机数1` 进行加密，并将加密后的内容发送给客户端
1. 客户端收到加密内容，使用自己的私钥进行解密，得到`随机数1`。
1. 客户端将连接过程中生成的`通信密钥`和`随机数1`结合，计算其 `MD5`，并将结果发送给服务端。
1. 服务端使用相同方式计算 `MD5`，对比客户端传回的 `MD5`，如果匹配，则通过验证，允许登录，不要求密码

## 小结

连接过程与 TLS 连接类似，事实上目前互联网所有涉及加密的通信原理都类似，基本都离不开 [PKI 体系](https://en.wikipedia.org/wiki/Public_key_infrastructure)

保存一份公钥，意味着你信任对应的私钥方，有时候你从某网站拿到的公钥并不是真正的公钥，可能已经被中间人替换掉，但证书指纹是无法伪造的，因此有些网站会将自己公钥证书的指纹公示在显眼的位置，以便使用者对比指纹，判断自己得到的公钥是否被替换过。总之，面对来历不明的公钥，我们需要多加注意。

密钥对一般包含公钥和私钥，公钥可以广泛传播，而私钥则需要小心保存。

公钥、私钥都可以进行加解密，但这容易产生误解，因此我们一般将公钥加密，私钥解密称为`加密和解密`，将私钥加密，公钥解密称为`签名和验证签名`。加密是为了数据安全，签名是为了向对方证明身份（我真的是私钥拥有者鸭）。

本文验证过程与很多文章不同，主要参考了 [Understanding the SSH Encryption and Connection Process](https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process) ，如有不对的地方，欢迎批评指正。

## 参考链接

[Understanding the SSH Encryption and Connection Process](https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process)