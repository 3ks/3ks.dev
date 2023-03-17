---
title: 使用 docker-compose 安装 elasticsearch
thumbnail: ''
disqusId: some-disqus-id
categories:
  - docker
  - elasticsearch
  - es
tags:
  - es
date: 2021-03-04 21:46:49
---

使用 docker-compose 安装 elasticsearch

<!-- more -->

## 一个代码片段

{% codeblock "docker-compose.yaml" lang:yaml >folded  %}
version: '3'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.11.1
    container_name: es01
    environment:
      - node.name=es01
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - /cs/es/data:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
      - 9300:9300
    networks:
      - elastic
    networks:
      - elastic

networks:
  elastic:
    driver: bridge
{% endcodeblock %}


## 问题

https://www.elastic.co/cn/webinars/getting-started-kibana

### 1 elasticSearch报错

参考：[docker下安装elasticSearch报错：[1]: max virtual memory areas vm.max_map_count [65530] is too low](https://blog.csdn.net/BigData_Mining/article/details/108927120)

也可参考：[Set vm.max_map_count to at least 262144](https://www.elastic.co/guide/en/elasticsearch/reference/7.11/docker.html#_set_vm_max_map_count_to_at_least_262144)

### 2 kibana 修改语言

home -> manage -> kibana -> Advanced Settings -> Numeral language locale -> Simplified Chinese

## 参考链接

[docker安装ES & Kibana](https://www.jianshu.com/p/fdfead5acc23)

[Install Elasticsearch with Docker](https://www.elastic.co/guide/en/elasticsearch/reference/7.11/docker.html#docker-compose-file)