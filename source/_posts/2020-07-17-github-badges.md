---
title: 如何为你的 GitHub 项目添加 badge
thumbnail: ''
disqusId: c8c31844-fbcd-4928-b007-c97c9f39b66f
categories:
  - [Github,Badge]
tags:
  - GitHub
  - Badge
  - Markdown
date: 2020-07-17 15:37:37
---

我们在很多开源项目的 README 都能看到大量的徽章（badge），这些徽章大都挺漂亮的，最重要的是，通过这些徽章，我们可以快速的获取项目的状态信息，例如：开源协议、版本、单元测试覆盖率等。

![](https://img.shields.io/apm/l/vim-mode?style=for-the-badge)  ![](https://img.shields.io/github/v/release/kubernetes/kubernetes?style=for-the-badge)

那我们应该如何为自己的项目加上一些徽章呢？ 

<!-- more -->

## markdown 与图片 

### markdown 插入图片

我们都知道项目的 README 是一个 markdown 文件，在 markdown 内可以这样插入一张图片

```markdown
![](https://cdn.sguan.top/markdown/20200721141223.png)
```

其效果类似于这样：

![](https://cdn.sguan.top/markdown/20200721141223.png)

### markdown 插入图片链接

此外，如果我们想在点击图片后，跳转到某个页面，可以这样写：

```markdown
[![](https://cdn.sguan.top/markdown/20200721141223.png)](https://baidu.com)
```

其效果类似于这样：

[![](https://cdn.sguan.top/markdown/20200721141223.png)](https://baidu.com)

点一下图片试试，看看和前一张图片的区别。

## shields.io

徽章其实也只是一些图片，只不过它看起来会很小，包含了一些信息，并且一般是 svg 格式的。也就是说我们只要在 README.md 中引入这种风格的图片就可以了。

一个徽章的构成元素包括：标签、信息、风格样式：![](https://img.shields.io/github/v/release/kubernetes/kubernetes?style=for-the-badge)

上面这个徽章，它的标签是 `RELEASE`，信息是 `x.x.x` 的一个版本号，风格比较扁平化。

> 问题在于我们如何生成符合自己需求的图片呢？

答案是 [shields.io](https://shields.io)，目前我们在 GitHub 上看到的很多徽章都是在这个网站生成的，该网站提供了数以百计的徽章可以选择，并且你还可以自定义徽章，这足以满意绝大部分开发者的需求。生成的图片都会有一个唯一地址，你可以直接引用该图片，或者下载图片，将其保存到项目中，再通过相对目录引用图片。无论如何，我们需要做的只是生成想要的图片、在 markdown 中引入它。

虽然有很多的徽章可以选择，但一般只需要为重要的信息添加徽章，过多的徽章会让读者无法快速提取其中的信息。至于哪些是重要的信息，这取决于具体的项目以及你自身的理解。

## 静态徽章

对一个项目来说，某些徽章基本上不会发生变化，例如：开源协议、一些链接等。

这种徽章可以看成是静态徽章，即不会变化的徽章。

### 开源协议

shields.io 提供了大量的静态徽章，这里我们以 `开源协议` 系列的徽章为例。

开源协议相关的徽章可以在 [这里](https://shields.io/category/license) 看到。

![shields 提供的开源协议](https://cdn.sguan.top/markdown/20200721161049.png)

点击你需要的协议，然后填入参数（这里我其实没太明白规则，一般填入 `e.g.` 后面的字符即可），例如：

![生成徽章](https://cdn.sguan.top/markdown/20200721162121.png)

红色圈出来的部分就是我们生成的徽章，点击下面的 `Copy Badge URL` 即可复制徽章图片的地址，得到的地址类似于这样：

```url
https://img.shields.io/aur/license/android-studio
```

你也可以点击 `⬇` 展开，复制其它格式的地址，不同格式的地址本质上并没有区别，例如 markdown 格式的徽章地址。

```
![AUR license](https://img.shields.io/aur/license/android-studio)
```

### 调整风格

此外，你可以修改参数，生成不同风格的徽章。

![style](https://cdn.sguan.top/markdown/20200721162822.png)

生成的徽章就会是风格比较扁平化的徽章。![](https://img.shields.io/aur/license/android-studio?style=for-the-badge)

### 自定义徽章

如果预置的徽章都不能满足你的要求，你还可以生成自定义徽章。

[shields.io](https://shields.io) 每个页面的底部都提供了自定义徽章的功能，你可以根据自己的需要填入信息、生成徽章、引用。

https://img.shields.io/badge/ID-3ks-brightgreen?style=for-the-badge

![](https://cdn.sguan.top/markdown/20200721164249.png)

https://img.shields.io/static/v1?label=NAME&message=Guan&color=brightgreen?style=for-the-badge

https://img.shields.io/static/v1?label=<LABEL>&message=<MESSAGE>&color=<COLOR>

## 动态徽章

### 预置

### 自定义


## 参考链接

[shields.io](https://shields.io)

[GitHub 项目徽章的添加和设置](https://juejin.im/entry/5907fa59570c3500582d326c)

[如何让你的 GitHub 项目表面上更专业](https://juejin.im/post/5d312fb6f265da1b60293c51)

[为你的Github生成漂亮的徽章和进度条](https://shikieiki.github.io/2017/03/01/%E4%B8%BA%E4%BD%A0%E7%9A%84Github%E7%94%9F%E6%88%90%E6%BC%82%E4%BA%AE%E7%9A%84%E5%BE%BD%E7%AB%A0%E5%92%8C%E8%BF%9B%E5%BA%A6%E6%9D%A1/)

[Adding a workflow status badge to your repository](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#adding-a-workflow-status-badge-to-your-repository)