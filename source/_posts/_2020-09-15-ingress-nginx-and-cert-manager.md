---
title: 使用 helm 安装 ingress-nginx 和 cert-manager
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2020-09-15 19:44:18
---

一篇船新的博客

<!-- more -->

需要先安装 helm

## 安装 Ingress-Nginx Controller

添加 resp：

```bash
$ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
$ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
$ helm repo update
```

安装 Controller：

```bash
$ helm install [RELEASE_NAME] ingress-nginx/ingress-nginx
```

查看 svc：

```bash
$ kubectl get svc
NAME                                       TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                      AGE
kubernetes                                 ClusterIP      10.63.240.1     <none>           443/TCP                      23m
quickstart-nginx-ingress-controller        LoadBalancer   10.63.248.177   203.0.113.2      80:31345/TCP,443:31376/TCP   16m
quickstart-nginx-ingress-default-backend   ClusterIP      10.63.250.234   <none>           80/TCP                       16m
```

`controller` 默认 TYPE 是 LoadBalancer，可以根据自己的环境将其更改为 NodePort。

## 安装 cert-manager

创建、添加相关信息：

```yaml
$ kubectl create namespace cert-manager
$ helm repo add jetstack https://charts.jetstack.io
$ helm repo update
```


安装：

```bash
$ helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v1.0.0  --set installCRDs=true
```

查看状态：

```bash
$ kubectl get pods --namespace cert-manager
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-7884ffb949-hsg2k              1/1     Running   0          12h
cert-manager-cainjector-686c4f6c6d-wgm55   1/1     Running   1          12h
cert-manager-webhook-595c7544f5-r7gdj      1/1     Running   0          12h
```

## 配置 Let’s Encrypt Issuer

> 前提：已有一个可正常访问的 service，我这里以 monitoring 命名空间下的 grafana 服务为例。

{% codeblock "your-issuer.yaml" lang:yaml %}
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
 namespace: monitoring
 name: letsencrypt-prod
spec:
 acme:
   server: https://acme-v02.api.letsencrypt.org/directory
   email: user@example.com # 你的邮箱，由于提示证书过期等
   # 存储 ACME 获取到私钥的 secret 名
   privateKeySecretRef:
     name: letsencrypt-prod
   solvers:
   - http01:
       ingress:
         class: nginx
{% endcodeblock %}

创建资源：

```bash
$ kubectl apply -f your-issuer.yaml
```

查看状态：

```bash
$ kubectl get issuer letsencrypt-prod
NAME               READY   AGE
letsencrypt-prod   False   1m
```

如果一切顺利，其状态会在几分钟内变为 `True`

## 部署 TLS Ingress

准备资源：

> 注意资源的 namespace

{% codeblock "service-ingress.yaml" lang:yaml %}
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: grafana
  namespace: monitoring
  annotations:
    kubernetes.io/ingress.class: "nginx"    
    cert-manager.io/issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
    - grafana.3ks.dev
    secretName: grafana-s-secret
  rules:
  - host: grafana.3ks.dev
    http:
      paths:
      - path: /
        backend:
          serviceName: monitor-grafana
          servicePort: 80
{% endcodeblock %}

创建资源：

```bash
$ kubectl apply -f service-ingress.yaml
```

查看状态：

```bash
$ kubectl get certificate --namespace monitoring
NAME                     READY   SECRET                   AGE
grafana                  True    grafana-s-secret         1m
```

待状态变为 True，我们就可以尝试访问对应的域名，检查证书是否正确签发：

![](https://cdn.sguan.top/markdown/20200916121638.png)

![](https://cdn.sguan.top/markdown/20200916121735.png)

对于其它需要暴露的服务，类似的，在相应的 namespace 下创建 `Issuer` 和 `Ingress` 两个资源即可。

## 参考链接

[ingress-nginx chart](https://github.com/kubernetes/ingress-nginx/tree/master/charts/ingress-nginx)

[Installing cert-manager with Helm](https://cert-manager.io/next-docs/installation/kubernetes/#installing-with-helm)

[Securing NGINX-ingress](https://cert-manager.io/next-docs/tutorials/acme/ingress/)

[Prometheus and Grafana installation](https://kubernetes.github.io/ingress-nginx/user-guide/monitoring/)
