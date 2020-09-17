---
title: 构建一条 pipeline
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2020-09-17 14:02:46
---

一篇船新的博客

<!-- more -->

## 一个代码片段

8782be89-1c98-48dc-8c24-df8beb8c2bda

http://jenkins.jenkins:8080/job/hello-world/build?token=8782be89-1c98-48dc-8c24-df8beb8c2bda

https://jenkins.3ks.dev/job/hello-world/build?token=8782be89-1c98-48dc-8c24-df8beb8c2bda



token-1

112d1ffc47fad74ef70fe9be000e88db50


 Hook executed successfully but returned HTTP 404 {"jobs":null,"message":"Did not find any jobs with GenericTrigger configured! If you are using a token, you need to pass it like ...trigger/invoke?token=TOKENHERE. If you are not using a token, you need to authenticate like http://user:passsword@jenkins/generic-webhook... "}
 
## 403 问题

在 GitLab 中配置 webhook 后，测试一直无法通过，找了很多解决方法，但由于版本原因，并不适用于我的情况。

最后看到了这篇教程，通过测试发现，通过 GET 方法调用 hook 地址可以成功触发流水线，但是 POST 方法则提示 403。

因此，需要安装插件，更改 Jenkins 的默认行为。

https://jenkins.3ks.dev/job/p2/build?token=abc

TODO 日后再说

## 参考链接

[name](https://github.com/3ks)

[解决 Url is blocked: Requests to the local network are not allowed](https://www.cnblogs.com/zhongyuanzhao000/p/11379098.html)

[解决Jenkins出现No valid crumb was included in the reques](https://www.jianshu.com/p/10b85bb75f66)

[ingres nginx ConfigMaps](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/)

[](https://jaminzhang.github.io/ci/cd/Jenkins-config-job-relating-pipeline-gitlab-webhook/)

