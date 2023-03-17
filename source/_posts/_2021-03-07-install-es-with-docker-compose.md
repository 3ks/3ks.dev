---
title: install-skywalking-with-docker-compose
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2021-03-07 00:45:10
---

一篇船新的博客

<!-- more -->

## 一个代码片段

/usr/share/elasticsearch

{% codeblock "docker-compose.yaml" lang:yaml >folded %}
version: '3'
services:
  es1:
    image: elasticsearch:7.5.1
    container_name: es1
    ports:
      - "9200:9200"
      - "9300:9300"
    environment:
      - "discovery.type=single-node"
    volumes:
      - /cs/sw/es/data:/usr/share/elasticsearch/data
      - /cs/sw/es/logs:/usr/share/elasticsearch/logs
    restart: always
  oap:
    image: apache/skywalking-oap-server:8.3.0-es7
    container_name: oap
    ports:
      - "12800:12800"
      - "11800:11800"
    environment:
      - "TZ=Asia/Shanghai"
      - "SW_STORAGE=elasticsearch"
      - "SW_STORAGE_ES_CLUSTER_NODES=es1:9200"
    restart: always
  sky-ui:
    image: apache/skywalking-ui:8.3.0
    container_name: sky-ui
    ports:
      - "80:8080"
    environment:
      - "TZ=Asia/Shanghai"
      - "SW_OAP_ADDRESS=oap:12800"
    restart: always
{% endcodeblock %}


## 参考链接

- https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-docker.html
- https://www.fecmall.com/topic/1173
- https://blog.csdn.net/asc2046/article/details/77409589

https://mp.weixin.qq.com/s?__biz=MzkyNzI1NzM5NQ==&mid=2247484756&idx=1&sn=6029ed72391e175ebef3de47bf9290a2&source=41#wechat_redirect
https://zhuanlan.zhihu.com/p/238206335

http://blog.mathjia.net/2020/05/22/elasticsearch-kibana-combat/

https://www.iszy.cc/posts/wn3g9x/