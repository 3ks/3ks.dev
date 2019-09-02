---
date: 2017-04-09T10:58:08-04:00
title: "CentOS"
description: "CentOS"
type: "post"
weight: 2
---

# 申请swap空间
云服务内存不够，也没有自动分配swap空间，我们可以自己分配swap空间作为缓存，感觉swap类似windows里的虚拟内存。

> 我因为没有swap空间，导致mysql服务器无法启动

- df -h
> 获取硬盘设备路径

- sudo dd if=/dev/vda1 of=/swapfile count=4096 bs=1MiB
> 分配空间4G
> 官方的`sudo fallocate -l 4G /swapfile`是错误的，评论区出人才！

- sudo chmod 600 /swapfile
> 权限设置

- sudo mkswap /swapfile
> 制作swap系统，如果成功会有一个类似下面的提示
```
Setting up swapspace version 1, size = 4194300 KiB
no label, UUID=b99230bb-21af-47bc-8c37-de41129c39bf
```
- sudo swapon /swapfile
> 启动swap

- free -m
> 可以看到swap是否已经在运行了

- sudo vi /etc/sysctl.conf
> 在文件末尾追加内容
```
/swapfile   swap    swap    sw  0   0
```
- 保存提出即可。


[How To Add Swap on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-centos-7)


# 清理内存
```
sync &&\
echo 1 > /proc/sys/vm/drop_caches &&\
echo 2 > /proc/sys/vm/drop_caches &&\
echo 3 > /proc/sys/vm/drop_caches
```

[linux 内存清理/释放命令](https://www.cnblogs.com/52linux/archive/2012/03/08/2385399.html)

# 启用 etho0
CentOS 在刚刚装装好的时候可能存在没有启用网卡的情况，这个时候我们需要手动添加一个网卡,步骤如下：

- cd /etc/sysconfig/network-scripts/
- cp ifcfg-ens33 ens33_bak
- mv ifcfg-ens33 ifcfg-eth0
- vi ifcfg-eth0   
 
修改`name`=`eth0`
修改`device`=`eth0`

修改`ONBOOT`=`YES`

- vi /etc/default/grub

在`GRUB_CMDLINE_LINUX`原有参数后添加 `net.ifnames=0 biosdevname=0`

- grub2-mkconfig   -o  /boot/grub2/grub.cfg
- reboot
- ip addr

![成功获取IP](http://cdn.sguan.top/markdown/20181121/3oXaX3jtuKGw.png?imageslim)

# 配置静态IP
> 如果需要配置静态IP，可以编辑文件

- vi /etc/sysconfig/network-scripts/ifcfg-eth0
内容如下
```
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="static"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPADDR=192.168.7.7  #IP地址
NETMASK=255.255.255.0 #子网掩码
BROADCAST=192.168.7.255 #广播地址
GATEWAY=192.168.7.2 #网关
HWADDR="00:0C:29:8A:10:DB" #MAC地址,必须与虚拟机提供的MAC相同
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="eth0"
UUID="2d92e919-5fdc-4e73-ba32-d61876f172d4"
DEVICE="eth0"
ONBOOT="yes"
IPV6_PRIVACY="no"
```
- systemctl restart network 

重启网络，查看IP

> 如果出现Failed to start LSB: Bring up/down networking 的问题，可能是network与NetworkManager冲突导致的，禁用NetworkManager即可。

- systemctl stop NetworkManager
- systemctl disable NetworkManager
- reboot

[CentOS7 Failed to start LSB: Bring up/down networking.解决方法](https://www.cnblogs.com/bonjov1/p/4323836.html)


# 配置DNS
如果没有DNS，电脑也是没法正常上网的
- vim /etc/NetworkManager/NetworkManager.conf
```
#在main部分添加 “dns=none” 选项，类似下面这样
[main]
plugins=ifcfg-rh
dns=none
```
- vim /etc/resolv.conf
根据自己的地区选择合适的DNS
```
nameserver 8.8.8.8
nameserver 61.139.2.69
```


# SELinux
CentOS7中，很多服务默认都会被SELinux阻止，如果程序异常应该能及时考虑到权限问题

1. 查看某程序权限,off为禁止，on为允许

`getsebool -a | grep git`

2.临时关闭 SELinux

`setenforce 0`


![mark](http://cdn.sguan.top/markdown/20190112/R9T7wHynmOQL.png?imageslim)
> 参考连接

[一文彻底明白linux中的selinux到底是什么](https://blog.csdn.net/yanjun821126/article/details/80828908)

[查看 SELinux状态及关闭SELinux](http://blog.51cto.com/bguncle/957315)


# Centos防火墙配置

1.开放8080端口  

firewall-cmd  --zone=public  --add-port=8080/tcp --permanent 

> --permanent永久生效，没有此参数重启后失效

2.重新载入防火墙策略

firewall-cmd  --reload

3.查看端口

firewall-cmd --permanent --zone=public --list-ports 

4.禁用8080端口???

firewall-cmd --zone= public --remove-port=8080/tcp --permanent

> 参考资料

[Linux firewall-cmd 命令详解](https://blog.csdn.net/GMingZhou/article/details/78090963)







# 修改主机名

> 直接修改 `/etc/hostname` 文件来修改主机名


 
# 修改时区

如果需要修改时区，CentOS7中引入了一个timedatectl工具，直接运行命令：

`timedatectl set-timezone  Asia/Shanghai`

也可以手动修改时区文件 

`cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime`

> 出现设置时区后，时间依然快了8小时的情况,执行下面的命令

`ln -s -f /usr/share/zoneinfo/Universal /etc/localtime`

> 参考资料

[CentOS 7 修改系统默认时区](https://my.oschina.net/imhuayi/blog/800202)

[CentOS 7系统时间与实际时间差8个小时](https://blog.csdn.net/lin521lh/article/details/78456654)





# 配置 yum 安装源

由于总所周知的原因，国内网速较慢，访问国外资源可以选择 `科学上网` 或者 `国内镜像`

1. 备份当前 yum 源

`sudo cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
`

2. 设置阿里云 yum 源

`sudo wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
`
> 如果没有安装`wget`，可以参考后面`一些常用工具的安装`，只是可能速度较慢，配置完安装源后，以后再使用 `yum` 速度就较快了

3. 添加EPEL源

`sudo wget -P /etc/yum.repos.d/ http://mirrors.aliyun.com/repo/epel-7.repo
`


4. 清理缓存并生成新的缓存
```
sudo yum clean all
sudo yum makecache
```

> 参考连接

[CentOS 7 使用阿里云的yum源](https://blog.csdn.net/skykingf/article/details/51953700)


[网易163-CentOS镜像使用帮助](https://mirrors.163.com/.help/centos.html)

[清华-CentOS 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/centos/)


# 一些常用工具的安装
在完成IP配置，并确保可以正常上网后，我们可以安装一些常用的工具

### wget
> 可以将`wget`看作一个下载器

`yum install -y wget  `


### vim 
> 可能是神器，也可能只是一个文本编辑器,需要安装三部分
```
yum  install -y  vim-enhanced
yum  install -y  vim-minimal
yum  install -y  vim-common
```

### ifconfig
> 在确保网卡正常获取到 IP 地址，可以上网之后，发现 ifconfig 提示 ` ifconfig command not found` 这可能是因为没有安装相关的软件 `net-tools`

` yum install -y net-tools `

### sshd
> 这个没有安装的可能性很低，云服务器如果没有安装sshd，用户都没有办法连接，
> 但是本地安装 CentOS 可能出现没有安装的情况。

`yum install sshd-server`


### 安装 Git

`yum install -y git `

### 安装 Python 

` yum install -y python `












# SSH 登录很慢

> OpenSSH在用户登录的时候会验证IP，它根据用户的IP使用反向DNS找到主机名，再使用DNS找到IP地址，最后匹配一下登录的IP是否合法。如果客户机的IP没有域名，或者DNS服务器很慢或不通，会一直等到DNS超时，就会很花时间。（这种情况一般发生在本地没有域名的服务器）

> 修改 ssh 的配置文件，关闭DNS 和 GSSAPI验证即可加快登陆速度

- OpenSSH的主配置文件  /etc/ssh/sshd_config
```
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


# SSH 免密登录

1. 在服务器上生成用于登录的公钥和私钥

```ssh-keygen -t rsa```

首先提示输入生成文件的输出路径，然后是密码，最后是确认密码
> 密码是用于加密和登录时输入的密码，额，建议为空，不然还叫免密登录吗，但是请确保生成证书的安全，证书泄露可能很危险

> 生产环境强烈建议设置密码，毕竟稳定压倒一切,相当于双重验证吧

2. 导入公钥

```cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys```

3. 配置文件、文件夹的权限

```
#如果要开启严格模式，这一步需要正确配置权限
#如果不开启严格模式，可以跳过
sudo chmod -R 0700  ~/.ssh
sudo chmod -R 0600  ~/.ssh/authorized_keys
sudo chown -R guan:guan /home/guan
```

4.如果启用了SELinux，还需要执行（root用户把/home改成/root）
```
restorecon -R -v /home
```

5.修改SSH配置文件，支持使用证书登录（root权限）

```
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

5. 客户端获取私钥
 可以直接打开私钥文件然后复制内容到客户端（比如windows）

6. 使用PuTTY Key Generator 生成适用于 Putty 的证书
![mark](http://cdn.sguan.top/markdown/20181121/k1dWVLBE7vkr.png?imageslim)

选中私钥文件，然后点击保存为私钥 `Save private key`

7. PuTTY 使用证书实现免密登录
 - 打开Putty，加载或者新建一个会话
 - 点击 `connection`->`data` , 输入 `Auto-login username` (自动登录的用户名)
 - `connection`->`ssh`->`auth` 在`Private key file for authentication`选择私钥文件
 - `保存会话`，然后直接点击会话即可免密登录
 
![mark](http://cdn.sguan.top/markdown/20181121/dzLoRV3Uzaao.png?imageslim)


> 服务器存公钥，客户端存私钥



### 多个服务器共用一个公钥
> 首先一个巨坑，如果直接复制粘贴公钥至另一台服务器，很可能出错，导致服务器拒绝登录

```
正确的姿势之一是使用有公钥的服务器ssh登录并将公钥传输至另一个服务器，可以避免上面的问题

ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub

感谢阮一峰大佬的教程，网上的水文找了一大堆，都没有说到点子上。
```

> 参考链接

[CentOS 7 SSH使用证书登录](https://my.oschina.net/liting/blog/600098)

[Putty server refused our key的三种原因和解决方法](https://blog.51cto.com/callmepeanut/1336864)

[SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)









# putty乱码的问题
 上述问题是 putty 配置不正确导致的

> 回退，方向键等乱码问题，按下图箭头所指设置即可

![mark](http://cdn.sguan.top/markdown/20190112/FY22p1NYlmPR.png?imageslim)

> 小键盘等乱码问题，按下图设置箭头所指设置即可

![mark](http://cdn.sguan.top/markdown/20190112/dB1nfLMoj6jO.png?imageslim)


> 设置完不要忘记保存session


# 配置别名（快捷键）

> 在 Linux 中，可以通过 `alias` 设置一个别名，比如 `alias c='clear'` 执行命令后，输出`c`与`clear`的效果是相同的。但是这种方式设置的别名会在系统重启后失效，如果想要修改永久有效，可以:

- 修改用户目录下的`~/.bashrc`文件，这里修改的别名仅对设置的用户有效

- 修改 `/etc/bashrc`文件，这里修改的别名对所有用户都有效

> 这里设置了别名，每条命令需要以分号结尾。
```
alias sr='systemctl restart ';
alias ss='systemctl stop ';
```

> 修改，保存退出后，使用 `source 文件路径`使别名生效




# 安装Pip


```
# 添加软件源，即可使用yum安装pip
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum install python-pip
pip install --upgrade pip

```

![mark](http://cdn.sguan.top/markdown/20190322/boL3QOJtiwxe.png?imageslim)

> # 参考链接

[Centos7安装配置ss客户端](https://blog.whsir.com/post-2711.html)





# CentOS支持NTFS
- 下载组件
```
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum update
yum install ntfs-3g
安装完成后，正常挂载即可
特别注意：如果centos不支持USB3.0请使用2.0的接口，否则会报错！
```

- 挂载硬盘
```
mount -t ntfs-3g  /硬盘路径   /挂载路径
如：mount -t ntfs-3g /dev/sdc1 /mnt/usb 
卸载硬盘
umount  /挂载路径
如：umount /mnt/usb
```

- 开机自动挂载(硬盘路径应保持不变)
```
#备份配置文件
cp /etc/fstab  /etc/fatab.bak
#添加配置信息
vi /etc/fstab
文件尾添加
/dev/sdb1  /mnt/usb ntfs-3g  defaults 0 0
```
> Q:开机自动进入紧急模式(welcome to emergency mode! )
A:可能因为自动挂载的硬盘找不到了
https://www.linuxidc.com/Linux/2017-08/146347.htm


# 意外断电

导致出现错误，不能进入系统

> ## 参考链接

[VMWare的CentOS虚拟机断电或强制关机出现问题](https://www.jianshu.com/p/cc0b5d411f2b)