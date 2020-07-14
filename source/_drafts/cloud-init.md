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

# 注意事项

### 执行次数

cloud-init 的配置不是仅执行一次，而是每次开机都会执行。如果你只需让其完成一些初始化任务，可以在虚拟机首次启动后，将 cloud-init 关闭。或者直接修改 cloud-init 的配置。否则会出现手动修改一些配置，重启后总是会被重置的情况。

# 配置 root 的密码 

在 proxmox 中 root 配置默认密码，会导致密码被修改为不知道是什么（bug？或者我姿势不对）。
解决方法是用另一个管理员用户重置 root 密码，并删除 cloud-init 配置 root 密码的相关部分。

# 安装 cloud-init

```bash
yum -y install cloud-init
```

# 配置 hostname

在 proxmox 的 cloud-init 中，`proxmox -> 选项 -> 名称` 会作为虚拟机的 hostname

# 配置项

刚刚了解 cloud-init，本人仅用其进行 ip hostname 等设置，其它的大量的配置项我个人更喜欢在制作 `模板` 时全部准备好。

# pve 和 cloud-init

pve 模板配合 cloud-init 可以快速克隆出大量网络无冲突的虚拟机，很强大！

唯一不足的地方是，需要通过 pve web UI，在克隆出来的新机器上，修改一下 IP 和 hostname，不过这也比直接用命令行修改方便快捷。

# 参考链接

[Correct Installation and Configuration of pip2 and pip3](https://www.gungorbudak.com/blog/2018/08/02/correct-installation-and-configuration-of-pip2-and-pip3/)

[proxmox里使用cloud-init和一些笔记](https://zhangguanzhang.github.io/2019/01/22/proxmox-cloud-init/#%E5%AE%89%E8%A3%85%E5%AE%8Cproxmox%E7%9A%84%E4%B8%80%E4%BA%9B%E8%AE%BE%E7%BD%AE)

[Proxmox使用Cloud-Init制作Debian10系统模板](https://lala.im/6157.html)