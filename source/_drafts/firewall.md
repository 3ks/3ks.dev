---
title: "CentOS 防火墙配置"
date: 2020-02-22T16:35:25+08:00
tags: ["CentOS","firewall"]
draft: false 

---

# 开放端口

开放8080端口  

firewall-cmd  --zone=public  --add-port=8080/tcp --permanent 

> --permanent永久生效，没有此参数重启后失效

# 重新载入防火墙策略

firewall-cmd  --reload

# 查看开放端口情况

firewall-cmd --permanent --zone=public --list-ports 

# 禁用端口

禁用8080端口

firewall-cmd --zone= public --remove-port=8080/tcp --permanent

# 参考链接

[Linux firewall-cmd 命令详解](https://blog.csdn.net/GMingZhou/article/details/78090963)