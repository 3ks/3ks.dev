---
title: "Cloud-init"
date: 2020-03-15T12:44:01+08:00
tags: ["none",]
draft: true 

---

# 背景 

试想以下，现在我们想要装机多个类似的 CentOS 系统，有什么比较快的方法吗？
在 VMWare 或者 Virtual-Box 下，有一个很实用的功能——克隆
克隆出来的系统完全一致，包括我们需要的各种软件包等。
可以避免做一些重复的事情

但是克隆出来的系统还是有一些缺陷的，服务器系统一般会使用静态 IP 地址，而克隆出来的两个系统的 Hostname、IP、MAC 地址也会完全相同。
我们不得不再次手动的修改一下这部分内容，虽然不复杂，但总归是需要花点事件的。

有什么方法可以简化这个过程吗？有！—— Cloud-init

# Cloud-init

Cloud-init 用于创建虚拟机实例完成初始化操作，功能不仅限于上面的提到的设置 Hostname、IP 等，还可以设置是否允许密码登陆，包装软件包，硬盘分区/扩容等。

不过我个人觉得最实用的还是设置 Hostname、IP 等，至于其它的功能，我更喜欢在制作模板的时候就将其完成。

# Proxmox 下的 Cloud-init 使用

添加 cloud-init 
安装
配置
克隆

# 参考链接

[proxmox里使用cloud-init和一些笔记](https://zhangguanzhang.github.io/2019/01/22/proxmox-cloud-init/#%E5%AE%89%E8%A3%85%E5%AE%8Cproxmox%E7%9A%84%E4%B8%80%E4%BA%9B%E8%AE%BE%E7%BD%AE)

[Proxmox使用Cloud-Init制作Debian10系统模板](https://lala.im/6157.html)