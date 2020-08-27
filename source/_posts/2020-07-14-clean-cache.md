---
title: Linux 清理缓存
thumbnail: 'https://cdn.sguan.top/markdown/20200827141125.png'
disqusId: d3fa116c-c5de-11ea-87d0-0242ac130003
categories:
  - ["Linux","CentOS","cache"]
tags:
  - Linux
  - CentOS
  - cache
date: 2020-07-14 22:28:33
---

以下几条命令用于清理不同类型的缓存。

<!-- more -->

## 清除页面缓存（PageCache）

下面的命令只会清理掉页面缓存，对于生产环境来说是比较安全的。

```bash
$ echo 1 > /proc/sys/vm/drop_caches
```

## 清除目录项和 inode

```bash
$ echo 2 > /proc/sys/vm/drop_caches     
```

## 清除页面缓存、目录项和 inode

下面的命令会将缓存完全清除，随后可能导致系统变慢，因为很多应用程序的缓存失效后，会从数据源读取数据（例如硬盘、API 等）。

不建议在生成环境用使用该命令，除非你知道自己在做什么：

```bash
$ echo 3 > /proc/sys/vm/drop_caches     
```

## 注意事项

### 适用场景

建议仅在测试和调试场景使用。

清理该文件不应该作为控制内存使用的手段，缓存是由程序申请并使用的，它往往会带来程序效率的提升（如减少磁盘访问频率），内存开销过大一般应该从程序优化角度考虑。如果你强行清理掉缓存，反而可能带来性能损失，因为一些程序在缓存失效后，会消耗大量的 I/O 和 CPU 来重建缓存。

### 非破坏性的

实际上，上面的清除操作都不是破坏性的操作，即它们都不会清除脏对象，因此你不必担心未同步的文件会被清除掉。

但换个角度来看，这在一定程度上会减少清理缓存的效果，因为脏对象没有被清理。

此时我们可以在清除缓存前，执行 `sync` 命令，先同步文件，减少脏对象的数量，以提高清理效果。例如：

```bash
$ sync; echo 1 > /proc/sys/vm/drop_caches
```

## 问题（TODO）

虽然到处都在说 1、2、3 分别代表清除不同类型的缓存，但从来没有人说过为什么 echo 不同的数字会带来不同的效果。

其用法在 [Kernel Documentation](https://www.kernel.org/doc/Documentation/sysctl/vm.txt) （关键字 `drop_caches `） 可以找到。但同样只写了用法与效果，没有提到原理。

个人猜测这是 Kernel 拦截了对 drop_caches 文件的写入，并根据不同值做出不同处理

以后有机会再看看...

## 参考链接

[在 Linux 上如何清除内存的 Cache、Buffer 和交换空间](https://linux.cn/article-5627-1.html)

[Kernel Documentation](https://www.kernel.org/doc/Documentation/sysctl/vm.txt)

[Hibernate中的脏检查和缓存清理机制](https://www.cnblogs.com/zhangzongle/p/5782375.html)