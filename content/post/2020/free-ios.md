---
title: "使用 iOS 模拟器进行网页调试"
date: 2020-03-01T10:27:34+08:00
tags: ["iOS","前端","html","Xcode"]
draft: true 

---

# 背景

昨晚与一位开源大佬闲聊之余，大佬表示他的网站在 iOS 设备下字体显示有异常，让我帮忙看看能不能解决，想到自己有点前端基础就试着看了一下。

最初打算通过 Chrome/Safari 模拟设备重现问题，并修复。后来发现该问题只在 iOS 设备下出现。无奈，看样子必须要一个 iOS 设备才行。

网页在且只在 iOS 下出现一些异常。而你没有 iOS 设备。

好在苹果非常贴心的提供了 iOS 模拟器，即使没钱买设备，也可以进行调试。

# 目的

没有实体 iOS 设备，想进行 iOS 调试

# 条件

有一个 macOS 电脑 _(¦3」∠)_

# 实现过程

### 1 安装 Xcode

Xcode 是苹果官方提供的编辑器，可用于开发苹果系的所有应用，支持 Object-C, Swift。

打开 `Apple Store`，搜索 `Xcode`，点击 `获取`。

接着等待完成下载和安装，安装包大小约为 8 GiB，下载速度取决于你的网络状况。

### 2 创建项目

在完成 Xcode 的安装后，可以在启动台找到它，打开并创建一个项目。

由于我们是调试网页，并非苹果应用开发者，所有随便创建一个项目即可。

### 3 选择模拟设备并启动模拟器

在编辑器的左上角附近可以选择模拟器的设备类型、型号。

完成选择后，点击 `运行` 按钮即可启动模拟器。

### 4 开始调试前的设置
 
模拟器默认会运行刚刚创建的 app，我们用鼠标模拟返回桌面的手势，鼠标放在模拟器屏幕底部横条的下面，按住左键向上拖动即可返回桌面。

###### 4.1 设置模拟器语言（可选） 

依次选择：Settings -> General -> Language&Region -> iPhone Language -> 简体中文（或其它你想设置的语言）-> 确定（Change to xxx Language）

###### 4.2 启用 macOS safari 的开发者模式

打开电脑上的 safari 浏览器，依次选择：Safari 浏览器 -> 偏好设置 -> 高级 -> 在菜单栏中显示"开发"菜单

### 5 调试

###### 5.1 在模拟器中打开网页

模拟器应该是通过宿主机 NAT 接入网络的，它可以访问公网上的服务器、本机、本地局域网的 IP 地址，注意不要访问 127.0.0.1 及 localhost 等回环地址。

###### 5.2 选择调试设备 

在 Safari 的菜单栏中依次选择：开发 -> 模拟器 iPhone xxx -> 当前访问网页地址

如果 `开发` 选项下没有找到模拟器，可能是因为 Safari 在模拟器之前打开了，退出并重新打开 Safari 即可。

###### 5.3调试
 
弹出元素审查，接下来就是我们熟悉的调试过程了，同学们根据自己的问题进行调试即可。

# 总结

这是苹果应用的基本调试过程，真实设备流程类似，只是在选择设备那里，不是选择模拟器，而是选择真实的设备（我猜的）。

对于习惯通过 Chrome 调试的同学，可能需要一个适应 Safari 的过程。

模拟器没有 App Store，意味只能安装/调试自己开发的应用，以及基于 Safari 调试网页。

模拟器设备很丰富，有 iPhone/ iPad/ watchOS/ TvOS 等多种设备的多种型号可供选择。

模拟器系统版本能跟上大版本，我调试时（2020-03-01）使用的是 iOS 13.3

# 扩展阅读

对于只进行网页调试的同学来说，每次先打开 Xcode 再运行模拟器显得步骤有点多余。

其实苹果的模拟器是一个单独的应用，名为 `Simulator`，我们是可以直接运行该应用并进行网页调试的。

这里介绍两种方法，二选一即可单独运行 Simulator。

### 1 在程序坞中保留

启动模拟器后，在程序坞列表找到 Simulator，然后点击右键，并依次选择：选项 -> 在程序坞中保留

这样即使退出 Xcode 和模拟器，下次也可以在在程序坞中找到并直接运行它。

### 2 添加至启动台

Simulator 是一个单独的应用，只是默认没有添加至启动台，运行下面这条命令即可将其添加至启动台：

```bash
ln -s /Applications/Xcode.app/Contents/Developer/Applications/Simulator.app /Applications/Simulator.app
```

> 注意：这两种方法都需要，经过 Xcode 启动模拟器至少一次后才能正常使用，因为 Xcode 会在首次运行时初始化一些数据。 

# 参考链接

[在 Mac 上拍摄截屏](https://support.apple.com/zh-cn/HT201361)

[iphone ipad CSS font-size 異常 解決方法](https://www.ez2o.com/Blog/Post/iphone-ipad-font-size-css-bug-fix)

[使用 Xcode 在 iOS 模拟器中调试 Web 页面](https://blog.csdn.net/u010471289/article/details/80212640)

[Xcode模拟iPhone教程!](https://blog.csdn.net/github_38885296/article/details/78857451)

[在 Mac 上拍摄截屏](https://support.apple.com/zh-cn/HT201361)

