---
title: Linux 清理缓存
disqusId: d3fa116c-c5de-11ea-87d0-0242ac130003
categories:
  - ["Linux","CentOS","cache"]
tags:
  - Linux
  - CentOS
  - cache
date: 2020-07-14 22:28:33
---

万物皆文件，清空文件即清理缓存，执行下面的命令即可：

<!-- more -->

```bash
$ sync && echo 1 > /proc/sys/vm/drop_caches && echo 2 > /proc/sys/vm/drop_caches && echo 3 > /proc/sys/vm/drop_caches
```

## 参考链接

[linux 内存清理/释放命令](https://www.cnblogs.com/52linux/archive/2012/03/08/2385399.html)