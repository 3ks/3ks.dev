---
title: "Git 的一些用法"
date: 2020-02-22T17:33:41+08:00
tags: ["git",]
draft: true 

---

# .gitignore 忽略.idea无效的问题

参考连接 [idea git无法忽略.idea,target文件夹及.iml文件](https://my.oschina.net/u/3316387/blog/1635593)

# git 项目代码贡献统计

```bash
git log --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done
```

# 项目代码总行数

```bash
find . "(" -name "*.go" ")" -print | xargs wc -l
```
