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

[基于docker部署skywalking实现全链路监控](https://www.cnblogs.com/xiao987334176/p/13530575.html)

[SkyWalking OAP](https://hub.docker.com/r/apache/skywalking-oap-server)

[Current image doesn't Elasticsearch 6](https://github.com/apache/skywalking/blob/master/docs/en/setup/backend/backend-storage.md#elasticsearch)