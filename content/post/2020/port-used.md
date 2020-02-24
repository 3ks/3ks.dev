---
title: "Windows 下某个端口已被占用的问题"
date: 2020-02-22T17:08:04+08:00
tags: ["windows","port"]
draft: false 

---

# 端口已被占用 1080

在windows平台下启动某个软件，有时候会出现如下错误，提示端口已经被占用。

![mark](https://cdn.sguan.top/markdown/20190527/72e0StnM4aNU.png?imageslim)

> 结束掉占用端口的程序即可

- win+r

- cmd

- netstat -aon|findstr "1080"

> 查看占用端口的程序，其中最后一列表示程序PID，打开任务管理器，找到PID对应的程序关闭即可。

![mark](https://cdn.sguan.top/markdown/20190527/3JfTEpmK59xe.png?imageslim)

> 任务管理器内对应的程序已经被结束掉了，下次再截图吧。

- 如果任务管理内找不到 PID 列的同学，可以右键字段列表，选择 `PID` 即可

![mark](https://cdn.sguan.top/markdown/20190527/33kqSFqlDAmh.png?imageslim)
