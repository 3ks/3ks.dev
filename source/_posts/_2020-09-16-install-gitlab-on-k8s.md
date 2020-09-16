---
title: 在 Kubernetes 内安装 GitLab
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2020-09-16 12:28:29
---

一篇船新的博客

<!-- more -->

## 部署 Redis

{% codeblock "git-redis.yaml" lang:yaml >folded %}
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  namespace: git
  name: git-redis-pvc
spec:
  storageClassName: nfs-client
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
---
apiVersion: v1
kind: Service
metadata:
  name: redis
  namespace: git
  labels:
    name: redis
spec:
  ports:
    - name: redis
      port: 6379
  selector:
    name: redis               
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  namespace: git
  labels:
    name: redis
spec:
  selector:
    matchLabels:
      name: redis
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        name: redis
    spec:
      containers:
      - name: redis
        image: sameersbn/redis
        imagePullPolicy: IfNotPresent
        ports:
        - name: redis
          containerPort: 6379
        volumeMounts:
        - mountPath: /var/lib/redis
          name: data
        livenessProbe:
          exec:
            command:
            - redis-cli
            - ping
          initialDelaySeconds: 30
          timeoutSeconds: 5
        readinessProbe:
          exec:
            command:
            - redis-cli
            - ping
          initialDelaySeconds: 5
          timeoutSeconds: 1
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: git-redis-pvc
```
{% endcodeblock %}

## 部署 Postgresql

{% codeblock "git-postgresql.yaml" lang:yaml >folded %}
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  namespace: git
  name: git-postgresql-pvc
spec:
  storageClassName: nfs-client
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
---
apiVersion: v1
kind: Service
metadata:
  name: postgresql
  namespace: git
  labels:
    name: postgresql
spec:
  ports:
    - name: postgres
      port: 5432
  selector:
    name: postgresql
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgresql
  namespace: git
  labels:
    name: postgresql
spec:
  selector:
    matchLabels:
      name: postgresql
  strategy:
    type: Recreate
  template:
    metadata:
      name: postgresql
      labels:
        name: postgresql
    spec:
      containers:
      - name: postgresql
        image: sameersbn/postgresql:11-20200524
        imagePullPolicy: IfNotPresent
        env:
        - name: DB_USER
          value: gitlab
        - name: DB_PASS
          value: passw0rd
        - name: DB_NAME
          value: gitlab_production
        - name: DB_EXTENSION
          value: pg_trgm,btree_gist
        ports:
        - name: postgres
          containerPort: 5432
        volumeMounts:
        - mountPath: /var/lib/postgresql
          name: data
        livenessProbe:
          exec:
            command:
            - pg_isready
            - -h
            - localhost
            - -U
            - postgres
          initialDelaySeconds: 30
          timeoutSeconds: 5
        readinessProbe:
          exec:
            command:
            - pg_isready
            - -h
            - localhost
            - -U
            - postgres
          initialDelaySeconds: 5
          timeoutSeconds: 1
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: git-postgresql-pvc
```
{% endcodeblock %}

## 部署 Gitlab

{% codeblock "gitlab.yaml" lang:go >folded %}
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  namespace: git
  name: git-web-pvc
spec:
  storageClassName: nfs-client
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
---
apiVersion: v1
kind: Service
metadata:
  name: gitlab-web
  namespace: git
  labels:
    name: gitlab-web
spec:
  ports:
    - name: http
      port: 80
    - name: ssh
      port: 22
  selector:
    name: gitlab-web
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gitlab-web
  namespace: git
  labels:
    name: gitlab-web
spec:
  selector:
    matchLabels:
      name: gitlab-web
  template:
    metadata:
      name: gitlab-web
      labels:
        name: gitlab-web
    spec:
      containers:
      - name: gitlab-web
        image: sameersbn/gitlab:13.3.4
        imagePullPolicy: IfNotPresent
        env:
        - name: TZ
          value: Asia/Shanghai
        - name: GITLAB_TIMEZONE
          value: Beijing
        - name: GITLAB_SECRETS_DB_KEY_BASE
          value: 0c89fecb-49e2-4938-a080-ea428de4d5ba
        - name: GITLAB_SECRETS_SECRET_KEY_BASE
          value: 6e9c4891-a309-4ae6-a706-29704430c228
        - name: GITLAB_SECRETS_OTP_KEY_BASE
          value: a3f41383-0222-4252-acb4-6b8ac180fd68
        - name: GITLAB_ROOT_PASSWORD
          value: admin123
        - name: GITLAB_ROOT_EMAIL
          value: 1@linux.com
        - name: GITLAB_HOST
          value: git.3ks.dev
        - name: GITLAB_PORT
          value: "80"
        - name: GITLAB_SSH_PORT
          value: "22"
        - name: GITLAB_NOTIFY_ON_BROKEN_BUILDS
          value: "true"
        - name: GITLAB_NOTIFY_PUSHER
          value: "false"
        - name: GITLAB_BACKUP_SCHEDULE
          value: daily
        - name: GITLAB_BACKUP_TIME
          value: 01:00
        - name: DB_TYPE
          value: postgres
        - name: DB_HOST
          value: postgresql
        - name: DB_PORT
          value: "5432"
        - name: DB_USER
          value: gitlab
        - name: DB_PASS
          value: passw0rd
        - name: DB_NAME
          value: gitlab_production
        - name: REDIS_HOST
          value: redis
        - name: REDIS_PORT
          value: "6379"
        ports:
        - name: http
          containerPort: 80
        - name: ssh
          containerPort: 22
        volumeMounts:
        - mountPath: /home/git/data
          name: data
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: git-web-pvc
```
{% endcodeblock %}

        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 180
          timeoutSeconds: 5
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          timeoutSeconds: 1

## 准备 Issuer 和 Ingress

{% codeblock "ingress.yaml" lang:yaml >folded %}
```yaml
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
 name: gitlab-pro
 namespace: git
spec:
 acme:
   server: https://acme-v02.api.letsencrypt.org/directory
   email: 1@linux.com
   privateKeySecretRef:
     name: gitlab-pro-secret
   solvers:
   - http01:
       ingress:
         class: nginx
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: gitlab
  namespace: git
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/issuer: "gitlab-pro"
    nginx.ingress.kubernetes.io/backend-protocol: "HTTP"
spec:
  tls:
  - hosts:
    - git.3ks.dev
    secretName: gitlab-pro-tls
  rules:
  - host: git.3ks.dev
    http:
      paths:
      - path: /
        backend:
          serviceName: gitlab-web
          servicePort: 80
```
{% endcodeblock %}

## 访问

默认用户名和密码分别是：`root` 和 `admin123`

![](https://cdn.sguan.top/markdown/20200916202550.png)

## 为何不使用 Helm

下载 chart

不使用 helm 的原因是 Gitlab 的 helm Chart 包含了太多不需要的组件，例如：CertManager、Registry 等。并且一些参数无法按预期的执行。

例如修改这些参数，但仍然不够，再直白点就是暂时玩不动。

{% codeblock "values.yaml" lang:go >folded https://www.google.com 查看源文件 %}
global.hosts.domain: git.3ks.dev
global.time_zone: UTC+8 
global.ingress.enabled: false 
global.ingress.configureCertmanager: false 
global.ingress.annotations."kubernetes.io/tls-acme": true 
certmanager.install: false
gitlab.webservice.ingress.tls.secretName: gitlab-pro-tls
registry.ingress.tls.secretName: registry-pro-tls
minio.ingress.tls.secretName: minio-pro-tls
nginx-ingress.enabled: false
prometheus.install: false
gitlab-runner.install: false
{% endcodeblock %}

## 参考链接

[在 Kubernetes 上安装 Gitlab](https://www.qikqiak.com/post/gitlab-install-on-k8s/)

[permission denied to create extension "btree_gist"](https://github.com/sameersbn/docker-gitlab/issues/2214#issuecomment-679398194)

[GitLab cloud native Helm Chart](https://docs.gitlab.com/charts/)

[External cert-manager and Issuer (external)](https://gitlab.com/gitlab-org/charts/gitlab/blob/master/doc/installation/tls.md#external-cert-manager-and-issuer-external)

[Storage Guide](https://docs.gitlab.com/charts/installation/storage.html)

[Minimal Gitlab CE Helm installation](https://forum.gitlab.com/t/minimal-gitlab-ce-helm-installation/33557/3)