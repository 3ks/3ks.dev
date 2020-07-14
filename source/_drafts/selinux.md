---
title: "临时关闭 SELinux"
date: 2020-02-22T16:32:18+08:00
tags: ["CentOS","SELinux"]
draft: true 

---

# 背景

CentOS7中，很多服务默认都会被SELinux阻止，如果程序异常应该能及时考虑到权限问题


# 查看

查看某程序权限,off为禁止，on为允许

`getsebool -a | grep git`


# 临时关闭 SELinux

`setenforce 0` 

![mark](https://cdn.sguan.top/markdown/20190112/R9T7wHynmOQL.png?imageslim)

### 参考链接

[一文彻底明白linux中的selinux到底是什么](https://blog.csdn.net/yanjun821126/article/details/80828908)

[查看 SELinux状态及关闭SELinux](http://blog.51cto.com/bguncle/957315)