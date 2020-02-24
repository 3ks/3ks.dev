---
title: "Linux 命令别名"
date: 2020-02-22T16:58:43+08:00
tags: ["Linux","alias"]
draft: false 

---

# Linux 命令别名

在 Linux 中，可以通过 `alias` 设置一个别名，比如 `alias c='clear'` 执行命令后，输出`c`与`clear`的效果是相同的。但是这种方式设置的别名会在系统重启后失效，如果想要修改永久有效，可以:

- 修改用户目录下的`~/.bashrc`文件，这里修改的别名仅对设置的用户有效

- 修改 `/etc/bashrc`文件，这里修改的别名对所有用户都有效

> 这里设置了别名，每条命令需要以分号结尾。
>
```bash
alias sr='systemctl restart ';
alias ss='systemctl stop ';
```

修改，保存退出后，使用 `source 文件路径` 使别名配置生效