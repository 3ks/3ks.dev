---
title: 使用 helm 安装 jenkins
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2020-09-16 22:20:53
---

一篇船新的博客

<!-- more -->

## 创建 namespace

```bash
$ kubectl create ns jenkins
```

## 配置 issuer

{% codeblock "ingress.yaml" lang:yaml >folded %}
```yaml
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
 name: jenkins-pro
 namespace: jenkins
spec:
 acme:
   server: https://acme-v02.api.letsencrypt.org/directory
   email: 1@linux.com
   privateKeySecretRef:
     name: jenkins-pro-secret
   solvers:
   - http01:
       ingress:
         class: nginx
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: jenkins
  namespace: jenkins
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/issuer: "jenkins-pro"
    nginx.ingress.kubernetes.io/backend-protocol: "HTTP"
spec:
  tls:
  - hosts:
    - jenkins.3ks.dev
    secretName: jenkins-pro-tls
  rules:
  - host: jenkins.3ks.dev
    http:
      paths:
      - path: /
        backend:
          serviceName: jenkins
          servicePort: 8080
```
{% endcodeblock %}

## 安装 Jenkins

```bash
$ helm repo add jenkinsci https://charts.jenkins.io
$ helm repo update
$ helm install jenkins jenkinsci/jenkins --namespace jenkins
NAME: jenkins
LAST DEPLOYED: Wed Sep 16 22:38:21 2020
NAMESPACE: jenkins
STATUS: deployed
REVISION: 1
NOTES:
1. Get your 'admin' user password by running:
  printf $(kubectl get secret --namespace jenkins jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
2. Get the Jenkins URL to visit by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace jenkins -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=jenkins" -o jsonpath="{.items[0].metadata.name}")
  echo http://127.0.0.1:8080
  kubectl --namespace jenkins port-forward $POD_NAME 8080:8080

3. Login with the password from step 1 and the username: admin

4. Use Jenkins Configuration as Code by specifying configScripts in your values.yaml file, see documentation: http:///configuration-as-code and examples: https://github.com/jenkinsci/configuration-as-code-plugin/tree/master/demos

For more information on running Jenkins on Kubernetes, visit:
https://cloud.google.com/solutions/jenkins-on-container-engine
For more information about Jenkins Configuration as Code, visit:
https://jenkins.io/projects/jcasc/
```

安装完成后可以根据相关提示得到一些信息：

默认用户名： admin

密码使用名为 `jenkins` 的 secret 存储：

```bash 
$ printf $(kubectl get secret --namespace jenkins jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
GHACkDe5CB
```

使用账号密码登录：

![](https://cdn.sguan.top/markdown/20200916224428.png)

> 该 Chart 安装的 Jenkins 默认没有 Agent，在有 Pipeline 的时候，会动态的创建 Agent。

## 本地语言

Jenkins 自身不提供中文界面，但可以通过安装插件来支持。

依次点击：`Manage Jenkins`、`Manage Plugins`、`Available`

搜索并安装：`Localization: Chinese (Simplified)`

选择 `Install after Restart`

## 参考链接

[jenkinsci/helm-charts](https://github.com/jenkinsci/helm-charts/blob/main/charts/jenkins/README.md)

[jenkins转换显示语言为中文简体（jenkins汉化）。](https://blog.csdn.net/w294954902/article/details/82587295)

[jenkins报：反向代理设置有误](https://www.cnblogs.com/shengulong/p/6758370.html)
