---
title: "Pve 安装"
date: 2020-02-22T17:15:17+08:00
tags: ["pve","install"]
draft: true 

---

# 下载镜像

前往官网下载 [Proxmox VE](https://www.proxmox.com/en/downloads) 的 iso 镜像。

# 关闭 `未订阅` 提示

```bash
grep -P "!== 'Active'" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js &&
  sed -ri.bak  '/!== '"'Active'"'/{s#\([^)]+\)#(false)#}' /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js
```
 
# 修改 IP 

在安装时有可以通过 GUI 设置 IP，后续想在修改的话，可以参考 [proxmox 完美更改 IP](https://blog.csdn.net/seaship/article/details/88919421)

# 启用 BBR

pve 6.x 的内核版本足够高，不需要安装内核，可以通过一键脚本直接启动 BBR

```bash
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```

> 直接输入 4 启用 BBR 即可。

# 注意事项

web 管理界面 `必须通过 https 的方式访问`

# 参考链接

[Proxmox VE 5.2 安装图文教程](http://www.myxzy.com/post-144.html)

[安装完 proxmox 的一些设置](https://zhangguanzhang.github.io/2019/01/22/proxmox-cloud-init/#%E5%AE%89%E8%A3%85%E5%AE%8Cproxmox%E7%9A%84%E4%B8%80%E4%BA%9B%E8%AE%BE%E7%BD%AE)

[proxmox 完美更改 IP](https://blog.csdn.net/seaship/article/details/88919421)

[BBR+BBR魔改+Lotsever(锐速)一键脚本 for Centos/Debian/Ubuntu](https://www.moerats.com/archives/387/)