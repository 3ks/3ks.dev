---
title: Hello World
thumbnail: 'https://cdn.sguan.top/makrdown/20200714210019.png'
disqusId: 7d1281cc-c59c-4886-8ef1-ee913b2acfde
date: 2020-07-14 22:03:46
---

欢迎 `来到` [Hexo](https://hexo.io/)!

<!-- more -->

> 引用文字

```flow
st=>start: 开始
op=>operation: My Operation
cond=>condition: Yes or No?
e=>end
st->op->cond
cond(yes)->e
cond(no)->op
&
```

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