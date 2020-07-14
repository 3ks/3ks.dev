---
title: Hello World
thumbnail: 'https://cdn.sguan.top/makrdown/20200714210019.png'
disqusId: 7d1281cc-c59c-4886-8ef1-ee913b2acfde
date: 2020-07-14 22:03:46
---

欢迎来到 [Hexo](https://hexo.io/)!

<!-- more -->

## shell 片段

```bash
echo "hello world"
```

## GO 代码

{% codeblock "main.go" lang:go https://www.google.com 查看源文件 %}
package main

import "fmt"

func main() {
	fmt.Println("hello world")
}
{% endcodeblock %}

## JSON 片段

```json
{
"title": "hello world",
"themes": "icarus"
}
```

## YAML 片段

{% codeblock "config.yml" lang:yaml %}
"title": "hello world"
"themes": "icarus"
{% endcodeblock %}