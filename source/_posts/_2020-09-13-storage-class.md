---
title: StorageClass 入门
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2020-09-13 17:21:41
---

StorageClass 用于在 Kubernetes 动态创建、分配 PV，本文记录了从安装存储实现到部署 Stateful 应用的过程。  

<!-- more -->

## 背景

[StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/) 让 PV 的创建和分配变得简单，但在使用之前，我们需要有一个存储实现以及相关的 provisioner。对于公有云环境，服务商一般都提供了现成的 provisioner，可以开箱即用。

而对于内部环境，我们得自己完成这个过程。


通过 NFS 提供 provisioner 能力的大致流程是：

- 使用一台主机来模拟存储服务器，在其上安装 NFS Server，并正确调整配置文件，在 Kubernetes 集群所有的 Worker 节点上安装 NFS Client。
- 在集群内安装 `nfs-client-provisioner` 插件，并配置 NFS Server 的相关信息（地址、路径等）
- 在创建 PVC 资源时指定 `storageClassName`，即可实现 PV 的自动创建与分配。

## NFS

### 服务端

对于 Debian 系系统，可以执行如下命令：

```bash
$ sudo apt-get update && sudo apt-get install -y nfs-kernel-server
```

创建一个需要暴露的目录，例如：

```bash
$ mkdir /nfs
```

编辑配置文件，在 `/etc/exports` 末尾追加如下内容：

{% codeblock "/etc/exports" lang:conf %}
/nfs  192.168.7.*(rw,sync,no_root_squash)
{% endcodeblock %}

> 如果不配置 no_root_squash 权限可能会导致 nfs 客户端出现无权限创建文件的情况。

重启服务：

```bash
$ systemctl restart nfs-kernel-server.service
```

### 客户端

为 Kubernetes 集群中的每个 Worker 节点安装 NFS Client

```bash
$ apt-get install -y nfs-common
```

完成安装后，查看 NFS 服务端可供挂载的目录：

```bash
$ showmount -e 192.168.7.19
Export list for 192.168.7.19:
/nfs 192.168.7.*
```

挂载目录：

```bash
$ mkdir -p /nfs
$ mount -t nfs 192.168.7.19:/nfs /nfs
```

查看 `/nfs` 的内容，并尝试创建文件，随后检查 NFS Server 端内容是否相同。

如果没有问题，我们可用卸载掉该目录：

```bash
$ umount /nfs
```

后续的挂载，读写均不需要认为操作。

## 安装 provisioner

现在我们回到 Kubernetes Client 主机上。

### 安装 helm

下载 [Helm](https://github.com/helm/helm/releases) 的二进制文件，并将其移至 PATH 目录下。

检查安装是否成功：

```bash
$ helm version
version.BuildInfo{Version:"v3.3.1", GitCommit:"249e5215cde0c3fa72e27eb7a30e8d55c9696144", GitTreeState:"clean", GoVersion:"go1.14.7"}
```

添加 helm 仓库：

```bash
$ helm repo add stable https://charts.helm.sh/stable
"stable" has been added to your repositories
```

我们应该会收到类似上面的提示。

### 安装 nfs-client-provisioner

该方法已弃用：https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner/issues/25#issuecomment-742616668

通过 helm 我们只需要执行一行命令即可完成 `nfs-client-provisioner` 的安装：

如果你需要自定义一些参数，可以参考：[Configuration](https://github.com/helm/charts/tree/master/stable/nfs-client-provisioner#installing-the-chart)

```bash
$ helm install <your-release-name> --set nfs.server=x.x.x.x --set nfs.path=/exported/path stable/nfs-client-provisioner
$ helm install nfs-1 --set nfs.server=172.26.32.31 --set nfs.path=/data/nfs stable/nfs-client-provisioner
```

观察 deployment 的状态：

```
$ watch kubectl get deployment
```

当 `nfs-1-nfs-client-provisioner` 的状态变为 `READY`，表示我们已经完成了 provisioner 的安装。

## 部署一个 Stateful 应用

### mysql

现在我们部署 Stateful 应用只需要声明 PVC、指定 StorageClass 即可，不需要再频繁维护 PV。（如果将其配置为默认存储后端的话，就只需要声明 PVC）

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
  name: mysql
spec:
  type: NodePort
  ports:
  - port: 3306
  selector:
    app: mysql
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
          name: mysql
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pvc
{% endcodeblock %}

执行命令：

```bash
$ kubectl apply -f mysql.yaml
```

稍等一会，应该可以看到 mysql 已经成功启动：

```bash
$ kubectl get deployment
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
mysql8                         1/1     1            1           108s
```

### 访问 mysql

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

### 查看 NFS 服务器

切换至 export 目录，查看文件列表：

```bash
$ ll
total 12
drwxr-xr-x  8 root             root  4096 Sep 13 14:59 ./
drwxr-xr-x 21 root             root  4096 Sep 13 05:24 ../
drwxrwxrwx  6 systemd-coredump root  4096 Sep 13 14:50 default-mysql-pvc-pvc-fe9f61ff-927d-4bb5-b2ca-91d1fff5a811
```

可以看到已经有了一个相关的文件夹，其中的内容也是 Mysql 数据目录的内容：

```bash
$ ll
total 186788
drwxrwxrwx 6 systemd-coredump root                 4096 Sep 13 14:50  ./
drwxr-xr-x 7 root             root                 4096 Sep 13 14:57  ../
-rw-r----- 1 systemd-coredump systemd-coredump       56 Sep 13 14:50  auto.cnf
-rw-r----- 1 systemd-coredump systemd-coredump  3104223 Sep 13 14:50  binlog.000001
-rw-r----- 1 systemd-coredump systemd-coredump      156 Sep 13 14:50  binlog.000002
-rw-r----- 1 systemd-coredump systemd-coredump       32 Sep 13 14:50  binlog.index
-rw------- 1 systemd-coredump systemd-coredump     1676 Sep 13 14:50  ca-key.pem
-rw-r--r-- 1 systemd-coredump systemd-coredump     1112 Sep 13 14:50  ca.pem
-rw-r--r-- 1 systemd-coredump systemd-coredump     1112 Sep 13 14:50  client-cert.pem
-rw------- 1 systemd-coredump systemd-coredump     1676 Sep 13 14:50  client-key.pem
-rw-r----- 1 systemd-coredump systemd-coredump   196608 Sep 13 14:52 '#ib_16384_0.dblwr'
-rw-r----- 1 systemd-coredump systemd-coredump  8585216 Sep 13 14:50 '#ib_16384_1.dblwr'
-rw-r----- 1 systemd-coredump systemd-coredump     5402 Sep 13 14:50  ib_buffer_pool
-rw-r----- 1 systemd-coredump systemd-coredump 12582912 Sep 13 14:50  ibdata1
-rw-r----- 1 systemd-coredump systemd-coredump 50331648 Sep 13 14:52  ib_logfile0
-rw-r----- 1 systemd-coredump systemd-coredump 50331648 Sep 13 14:50  ib_logfile1
-rw-r----- 1 systemd-coredump systemd-coredump 12582912 Sep 13 14:51  ibtmp1
drwxr-x--- 2 systemd-coredump systemd-coredump     4096 Sep 13 14:50 '#innodb_temp'/
drwxr-x--- 2 systemd-coredump systemd-coredump     4096 Sep 13 14:50  mysql/
-rw-r----- 1 systemd-coredump systemd-coredump 30408704 Sep 13 14:50  mysql.ibd
drwxr-x--- 2 systemd-coredump systemd-coredump     4096 Sep 13 14:50  performance_schema/
-rw------- 1 systemd-coredump systemd-coredump     1680 Sep 13 14:50  private_key.pem
-rw-r--r-- 1 systemd-coredump systemd-coredump      452 Sep 13 14:50  public_key.pem
-rw-r--r-- 1 systemd-coredump systemd-coredump     1112 Sep 13 14:50  server-cert.pem
-rw------- 1 systemd-coredump systemd-coredump     1676 Sep 13 14:50  server-key.pem
drwxr-x--- 2 systemd-coredump systemd-coredump     4096 Sep 13 14:50  sys/
-rw-r----- 1 systemd-coredump systemd-coredump 10485760 Sep 13 14:52  undo_001
-rw-r----- 1 systemd-coredump systemd-coredump 12582912 Sep 13 14:52  undo_002
```

## 配置默认 Storage Class

获取 StorageClass 名称：

```bash
 kubectl get storageclass
NAME         PROVISIONER                                  RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
logs         cluster.local/logs-nfs-client-provisioner    Delete          Immediate           true                   28h
data         cluster.local/nfs-1-nfs-client-provisioner   Delete          Immediate           true                   3d6h
```

指定为默认 Storage Class

```bash
$ kubectl patch storageclass <your-class-name> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
$ kubectl patch storageclass nfs-client -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

取消默认 Storage Class，只需要将最后的 `true` 变为 `false` 即可。 

```bash
$ kubectl patch storageclass <your-class-name> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
```

## 参考链接

[KUBERNETES存储之PERSISTENT VOLUMES简介](https://www.cnblogs.com/styshoo/p/6731425.html)

[PV、PVC、StorageClass讲解](https://www.cnblogs.com/rexcheny/p/10925464.html)

[深入浅出聊聊Kubernetes存储（一）：详解Kubernetes存储关键概念](http://dockone.io/article/8338)

[Ubuntu 16.04 安装nfs server](https://www.jianshu.com/p/5314f90330a6)

[Kubernetes NFS volume mount fail with exit status 32](https://stackoverflow.com/questions/34113569/kubernetes-nfs-volume-mount-fail-with-exit-status-32)

[NFS客户端挂载目录后无写入权限的解决方案](https://blog.csdn.net/iamonlyme/article/details/52089337)

[Installing nfs-client-provisioner Chart](https://github.com/helm/charts/tree/master/stable/nfs-client-provisioner#installing-the-chart)

[Helm Quickstart Guide](https://helm.sh/docs/intro/quickstart/)

[InnoDB: Table flags are 0 in the data dictionary but the flags in file ./ibdata1 are 0x4800!](https://www.jianshu.com/p/7af95b08b954)

[Run a Single-Instance Stateful Application](https://kubernetes.io/docs/tasks/run-application/run-single-instance-stateful-application/)

[改变默认 StorageClass](https://kubernetes.io/zh/docs/tasks/administer-cluster/change-default-storage-class/)