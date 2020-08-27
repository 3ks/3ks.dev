---
title: PicGo 新的图床工具
thumbnail: 'https://cdn.sguan.top/markdown/20200827171249.png'
disqusId: 670357a5-46fa-4d20-b3be-3ab446bac7ff
categories:
  - PicGo
tags:
  - PicGo
date: 2020-08-27 17:09:09
---

之前一直使用的图床工具是 MPic，虽然软件长期没有更新，但对我来说，MPic 足够的简单易用，并且是绿色软件，点开即用，使其看起来更加的轻量。

但是最近我发现 MPic 的[官网](http://mpic.lzhaofu.cn/)已经打不开了，这意味着其提供的 `预览和管理图片` 功能无法再使用，因此，我不得不重新寻找一款新的图床工具。

<!-- more -->

![MPic 官网](https://cdn.sguan.top/makrdown/20200504170254.png)

## PicGo

通过谷歌搜索 `图床工具` 可以很轻松的找到一系列的图床工具，其中 `PicGo` 引起了我的注意，这款工具的名称风格让我以为它是使用 Go 语言开发的。

显然我是想太多了，PicGo 是基于 `Electron-vue` 构建并开源的，不过 PicGo 看起来还是很不错的，它拥有很多的优点，特别是这三点：

- 支持 Windows, macOS, Linux。

- 支持 `快捷键上传` 剪切板里的第一张图片，且快捷键可以根据自己的习惯修改。

- 上传图片后 `自动复制链接` 到剪切板，且支持多种风格的链接（html、markdown、自定义风格等）。

基于这三点，PicGo 上传图片的操作异常简单：Ctrl + C（或者截图）-> 上传快捷键 -> Ctrl + V。然后我就可以得到一个 markdown 的图片链接。整个过程是如此的简单而灵活，必需给作者[一个赞](https://picgo.github.io/PicGo-Doc/zh/guide/#%E8%B5%9E%E5%8A%A9)。

## 使用方式

这里简单记录一下 Windows 平台下，使用 PicGo 作为图床工具，管理七牛云对象的过程。

### 下载并安装 PicGo

前往 https://github.com/Molunerfinn/PicGo/releases 根据自己的平台，选择下载最新的稳定版即可。如果你想尝鲜，也可以选择 Beta 版。

![下载 PicGo](https://cdn.sguan.top/makrdown/20200504181617.png)

然后双击安装程序，下一步，完成安装即可，没啥好说的。

### 图床配置

显示图床

PicGo 支持多种图床，而我目前只使用七牛云，对于其它用不到的图床，可以在 `PicGo 设置`界面将其隐藏：

![选择显示的图床](https://cdn.sguan.top/makrdown/20200504184126.png)

填写图床信息

![七牛云配置](https://cdn.sguan.top/makrdown/20200504184232.png)

这些配置项都取决于你自己的实际情况，如果你不清楚相关配置项应该如何填写的话，可以搜索一下入门文章或者七牛云官方文档。

## 一个坑

对于使用`七牛云`的同学，在设置图床时，`访问地址` 记得填上 `http://` 或者 `https://`。

![图床设置](https://cdn.sguan.top/makrdown/20200504174421.png)

虽然不填不会报错，图片也能正确上传，但这会影响到`生成的链接`和`图片预览`。

如果去掉访问地址的 `https://`，此时生成的图片链接会是这样：`![name](cdn.sguan.top/makrdown/20200504174627.png)`，虽然图片已经上传成功，但这个地址很多 markdown 渲染器是无法正确渲染的，需要我们手动加上 `https://`。

并且图片预览界面也会异常，无法正常显示这种图片。

![异常界面](https://cdn.sguan.top/makrdown/20200504175057.png)

## 总结

- MPic 相较于 PicGo 唯一的优点可能就是轻量，无需安装即可使用，其它的我个人觉得 PicGo 完胜。

- PicGo 支持插件，且拥有不错的生态，我对其未来充满信心，至少不会轻易跑路，遇到问题也方便寻求帮助（事实上，上面那个坑的解决方法就是在 PicGo 仓库的 [issue](https://github.com/Molunerfinn/PicGo/issues/79) 中找到的）。

- Mac 版的 GUI 比较漂亮，有毛玻璃效果，而 Windows 版就一言难尽了，相比之下比较难看。

- PicGo 是一款简洁而强大的图床工具。

# 参考链接

[PicGo](https://picgo.github.io/PicGo-Doc/zh/guide/)。

[上传图片后就看不到图片了](https://github.com/Molunerfinn/PicGo/issues/79)
