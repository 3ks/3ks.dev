---
title: Kubernetes 部署 Mysql
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2020-09-13 23:14:01
---

对于数据需要持久化存储的应用，我们可以为其挂载数据卷，来实现数据的持久化存储。

<!-- more -->

## Deployment

在拥有 StorageClass 的情况下，我们可以声明三个对象：PVC、Service、Deployment 来部署一个简单的服务。

{% codeblock "mysql.yaml" lang:yaml %}
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  storageClassName: nfs-client
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
apiVersion: v1
kind: Service
metadata:
  name: mysql8
spec:
  type: NodePort
  ports:
  - port: 3306
  selector:
    app: mysql8
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql8
spec:
  selector:
    matchLabels:
      app: mysql8
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql8
    spec:
      containers:
      - image: mysql:8.0
        name: mysql8
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: <your-password>
        ports:
        - containerPort: 3306
          name: mysql8
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pvc
{% endcodeblock %}

运行命令：

```bash
$ kubectl apply -f mysql.yaml
```

查看 Deployment 状态：

```bash
$ kubectl get deployment
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
mysql8                         1/1     1            1           110s
```

在 Deployment 可用后，我们可以直接访问相应的服务：

```bash
kubectl run -it --rm --image=mysql:8.0 --restart=Never mysql-client -- mysql -h mysql -p<your-password>
```

查看 Mysql 状态：

```
mysql> status;
--------------
mysql  Ver 8.0.21 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          13390
Current database:       mysql
Current user:           root@172.16.84.142
SSL:                    Cipher in use is ECDHE-RSA-AES128-GCM-SHA256
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.7.31 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mysql via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    latin1
Conn.  characterset:    latin1
TCP port:               3306
Binary data as:         Hexadecimal
Uptime:                 6 hours 12 min 23 sec

Threads: 3  Questions: 38049  Slow queries: 0  Opens: 142  Flush tables: 3  Open tables: 33  Queries per second avg: 1.702
--------------
```

## StatefulSet 部署

上一节我们通过 Deployment 部署了一个 mysql 实例，但在多 replica 的情况下，Deployment 已经无法满足需求了。

虽然 Deployment 应用也支持动态伸缩，但 Deployment 不保证 pod 每次挂载的 PVC/PV 都相同，这对于挂载了数据卷的应用来说是无法接受的。并且对于 Deployment 也不支持按顺序启动与停止。此时我们应该使用 StatefulSet。

TODO

{% codeblock "main.go" lang:go >folded https://www.google.com 查看源文件 %}

{% endcodeblock %}

## 参考链接

[StorageClass](https://www.qikqiak.com/k8s-book/docs/35.StorageClass.html#%E6%B5%8B%E8%AF%95)

[xtrabackup增量、全量备份mysql innodb教程](https://www.centos.bz/2017/09/xtrabackup-backup-mysql-innodb/)

[StatefulSet 和 Deployment 区别及选择方式](https://blog.csdn.net/nickDaDa/article/details/90401635)

[StatefulSet和Deployment的区别](https://www.cnblogs.com/weifeng1463/p/10284122.html)

[MySQL主从配置详解](https://www.jianshu.com/p/b0cf461451fb)

[Mysql主从基本原理，主要形式以及主从同步延迟原理 (读写分离)导致主库从库数据不一致问题的及解决方案](https://blog.csdn.net/helloxiaozhe/article/details/79548186)

[Run a Replicated Stateful Application](https://kubernetes.io/docs/tasks/run-application/run-replicated-stateful-application/)

[Kubernetes 部署 MySQL 集群](https://developer.aliyun.com/article/760332)
