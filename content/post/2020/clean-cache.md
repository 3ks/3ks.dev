---
title: "Linux 清理缓存"
date: 2020-02-22T16:03:41+08:00
tags: ["Linux","cache"]
draft: false 

---

# 清理内存

仅需两条命令即可：
```bash
sync && echo 1 > /proc/sys/vm/drop_caches
```

万物皆文件，清空文件即清理缓存

# 参考链接

[linux 内存清理/释放命令](https://www.cnblogs.com/52linux/archive/2012/03/08/2385399.html)