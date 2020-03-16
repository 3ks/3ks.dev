---
title: "批量导入导出 Docker 镜像"
date: 2020-03-16T21:09:29+08:00
tags: ["docker","docker images","export","import"]
draft: false 

---

# 批量导入导出 docker 镜像

### 批量导出

执行下面这条命令即可将所有镜像导出为单个文件 `all.tar.gz`

```bash
IMAGES_LIST=($(docker  images   | sed  '1d' | awk  '{print $1":"$2}')) && docker save ${IMAGES_LIST[*]}  -o  all.tar.gz
```

### 批量导入

上面导出的 `all.tar.gz` 文件传输至另一台机器上后，执行下面的命令即可将文件内的所有镜像导入：

```bash
docker load < all.tar.gz
```

# 参考链接

[逐个和批量导出导入docker镜像](https://blog.zhenglin.work/docker/save_load_images.html)