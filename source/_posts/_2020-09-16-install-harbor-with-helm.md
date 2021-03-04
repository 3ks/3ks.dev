---
title: 使用 helm 安装 harbor
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2020-09-16 21:20:30
---

一篇船新的博客

<!-- more -->

## 创建 namespace

```bash
$ kubectl create ns harbor
```

## 配置 issuer

{% codeblock "ingress.yaml" lang:yaml >folded %}
```yaml
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
 name: harbor-pro
 namespace: harbor
spec:
 acme:
   server: https://acme-v02.api.letsencrypt.org/directory
   email: 1@linux.com
   privateKeySecretRef:
     name: harbor-pro-secret
   solvers:
   - http01:
       ingress:
         class: nginx
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: harbor
  namespace: harbor
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/issuer: "harbor-pro"
    nginx.ingress.kubernetes.io/backend-protocol: "HTTP"
spec:
  tls:
  - hosts:
    - harbor.3ks.dev
    secretName: harbor-pro-tls
  rules:
  - host: harbor.3ks.dev
    http:
      paths:
      - path: /
        backend:
          serviceName: harbor
          servicePort: 80
```
{% endcodeblock %}

## 部署 Harbor
 
### 添加并下载 resp

```bash
$ helm repo add harbor https://helm.goharbor.io
$ helm repo update
$ helm fetch harbor/harbor
$ tar -zxvf harbor-x.x.x.tgz
```

### 配置 vlaues.yaml

```yaml
expose.type: clusterIP
externalURL: https://harbor.3ks.dev
harborAdminPassword: password
```

更多参数请参考：[configuration](https://github.com/goharbor/harbor-helm#configuration)

### 安装 Chart

```bash
$ helm install harbor ./ --namespace harbor
```

安装完成后，部署还需要几分钟时间，待所有 pod 正常后，即可访问：

![](https://cdn.sguan.top/markdown/20200916215851.png)


TODO: 扫描器


## 参考链接

[Helm Chart for Harbor](https://github.com/goharbor/harbor-helm)

[Vulnerability Scanning with Pluggable Scanners](https://goharbor.io/blog/harbor-1.10-release/#vulnerability-scanning-with-pluggable-scanners)

[Harbor镜像漏洞扫描](https://www.cnblogs.com/Dev0ps/p/12401615.html)
