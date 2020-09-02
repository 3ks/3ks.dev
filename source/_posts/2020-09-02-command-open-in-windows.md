---
title: 一条命令实现 Windows 下的“open”命令
thumbnail: 'https://cdn.sguan.top/markdown/20200902191337.png'
disqusId: 2c7d22ce-b683-4feb-9d77-21531f525574
categories:
  - [windows,command]
tags:
  - windows
  - command
  - open
date: 2020-09-02 19:11:31
---

在 MacOS 和带 GUI 的 Linux 下使用命令行时，可以通过 open 命令方便快捷的打开当前目录，在 Windows 下怎么实现呢？ 

<!-- more -->

## 命令

执行如下命令：

```bash
$ echo "alias open='explorer.exe'">> ~/.bashrc && source ~/.bashrc
```

随后执行命令查看是否有效：

```bash
$ open .
```

## explorer

在 Windows 下，图形界面的文件系统使用的是一个叫做 `exploere.exe` 的程序，我们看到的桌面图标、任务栏、我的电脑等都是由该程序提供的支持。

该程序也支持命令行调用，我们直接调用该程序，会打开资源管理器，例如：

```bash
$ explorer
```

如果带上参数 `.`，则相会打开当前路径：

```bash
$ explorer .
```

此外，还支持带上绝对、相对路径：

```bash
$ explorer "C:\Users"
$ explorer "Codes"
```

注意路径必须用引号包裹住

## 实现原理

本文就是为 `explorer` 增加了一个别名，实现了类似 MacOS 下 `open` 的功能。当然你也可以不使用别名，直接使用 explorer，只是拼写会麻烦一点。

另外，explorer 打开绝对路径不支持 `"/d/some/codes/ab"` 这种风格，必须是 `"D:\some\codes\ab"`，个人觉得不太方便。不过对于 `open .`，这已经足够了。 

explorer 的更多语法可以查看下面的参考链接。

## 参考链接

[Explorer.exe](https://ss64.com/nt/explorer.html)