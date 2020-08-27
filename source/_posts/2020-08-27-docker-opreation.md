---
title: docker 的一些操作
thumbnail: 'https://cdn.sguan.top/markdown/20200827153230.png'
disqusId: 70439d0c-c708-441c-bc9f-8edf2ccd4cfc
categories:
  - docker
tags:
  - docker
date: 2020-08-27 15:30:00
---

在使用 docker 时的一点及其，本质上来讲学好 linux 的命令，用任何软件都很简单。

<!-- more -->

## 筛选镜像

### docker image

`docker image` 的输出类似于这样，如果要批量操作镜像，我们一般会选择`镜像名：tag` 或者 `IMAGE ID` 的方式进行批量操作

```bash
$ docker images
REPOSITORY                                                     TAG                 IMAGE ID            CREATED             SIZE
alpine                                                         3.10.4              af341ccd2df8        2 months ago        5.56MB
redis                                                          5.0.5-alpine        ed7d2ff5a623        7 months ago        29.3MB
```

### 筛选镜像

`keyword` 表示镜像关键字，可以是镜像名、Tag、ID、体积等，总之是某个关键字，如果为 `.` 则匹配所有镜像：

```bash
$ docker  images | grep "keyword"
```

## 列出镜像

### 按镜像名列出

```bash
$ docker  images | grep "keyword"  | sed  '1d' | awk  '{print $1}'
```

### 按镜像名：tag列出

```bash
$ docker  images | grep "keyword"  | sed  '1d' | awk  '{print $1":"$2}'
```

### 按 IMAGE ID 列出

```bash
docker images | grep "keyword" | awk '{print $3}'
```

## 批量操作

### 批量导出镜像

按照 `镜像名：Tag` 的风格导出，以便在其它地方导入后可以方便的识别镜像名称

执行下面这条命令即可将所有包含关键字的镜像导出为单个文件 `all.tar.gz`

```bash
IMAGES_LIST=($(docker  images | grep "keyword"  | sed  '1d' | awk  '{print $1":"$2}')) && docker save ${IMAGES_LIST[*]}  -o  all.tar.gz
```

### 批量导入镜像

获取到一个镜像包后，（例如上面导出的 `all.tar.gz` ），执行 `docker load < 包名` 即可将包内的所有镜像导入至本机：

```bash
docker load < all.tar.gz
```

### 批量删除镜像

```bash
docker rmi $(docker images | grep "keyword" | awk '{print $3}') 
```

或者

```bash
docker rmi -f $(docker images | grep "keyword" | awk '{print $3}') 
```

如果遇到多个镜像拥有相同的 `IMAGE ID`，在第二种方式中，会强制删除所有所有与 `IMAGE ID` 匹配的镜像，而不论其他镜像是否与 `keyword` 匹配。

第一种方式则不会删除这种镜像

# 参考链接

[逐个和批量导出导入docker镜像](https://blog.zhenglin.work/docker/save_load_images.html)

[docker 如何批量删除镜像](https://blog.csdn.net/Ezreal_King/article/details/77285366)
