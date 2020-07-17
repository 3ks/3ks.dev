---
title: 重读 The Way to Go
thumbnail: ''
disqusId: 0601335d-0010-47df-9837-a5a905093f9b
categories:
  - [go,the-way-to-go]
tags:
  - go
date: 2020-07-17 11:13:16
---

`The Way to Go` 是一本经典的 Go 语言入门教学书籍，中文译本主要由 [unknown](https://github.com/unknown) 翻译，我已经阅读过多次了，每次都会有有一些收获，入坑 Go 正好两周年，正好最近在找工作，遂再次学习了本书（指译本，下同）的内容，并将本次的收获简单做一下记录。

<!-- more -->

## 阅读链接

项目地址：[unknwon/the-way-to-go_ZH_CN](https://github.com/unknwon/the-way-to-go_ZH_CN)

目录：[directory](https://github.com/unknwon/the-way-to-go_ZH_CN/blob/master/eBook/directory.md)

国内很多 Gopher 入门的第一本书就是 The Way to Go，据我所知，很多网站都能找到本书的内容。

如果你也对这本书感兴趣的话，我`只推荐`你通过上面的链接进行阅读。`The Way to Go` 这本书已经做出过多次修订，译文同样需要更新，在这方面，阅读译本原文可以获得最好的体验。

## 

{% codeblock "main.go" lang:go https://www.google.com 查看源文件 %}
package main

import "fmt"

func main() {
	fmt.Println("hello world")
}
{% endcodeblock %}
