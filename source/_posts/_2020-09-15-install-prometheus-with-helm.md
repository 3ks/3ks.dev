---
title: 使用 helm 安装 prometheus
thumbnail: ''
disqusId: some-disqus-id
categories:
  - todo
tags:
  - todo
date: 2020-09-15 16:43:01
---

一篇船新的博客

<!-- more -->

## 一个代码片段


拉取并解压 Chart：


```bash
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
$ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
$ helm repo update
$ helm pull prometheus-community/kube-prometheus-stack
$ tar -zxvf  kube-prometheus-stack-x.x.x.tgz
```

编辑 `values.yaml`

```yaml
prometheusOperator.tlsProxy.enabled: false 
prometheusOperator.admissionWebhooks.enabled: false 
prometheusOperator.admissionWebhooks.patch.enabled: false 
prometheusOperator.storage.volumeClaimTemplate.spec.storageClassName: <your-storage-class-name> 
prometheus.storage.volumeClaimTemplate.spec.storageClassName: <your-storage-class-name>
grafana.adminPassword: <your-grafana-web-ui-password>
```

安装：

```bash
$ helm install <release-name> ./ --namespace=monitoring
```

检查安装状态。

查看 Service，根据需要暴露服务即可。

```bash
$ kubectl get svc
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
alertmanager-operated                     ClusterIP   None             <none>        9093/TCP,9094/TCP,9094/UDP   82m
monitor-grafana                           ClusterIP   10.99.237.202    <none>        80/TCP                       82m
monitor-kube-prometheus-st-alertmanager   ClusterIP   10.101.120.13    <none>        9093/TCP                     82m
monitor-kube-prometheus-st-operator       ClusterIP   10.101.208.29    <none>        8080/TCP                     82m
monitor-kube-prometheus-st-prometheus     ClusterIP   10.106.245.45    <none>        9090/TCP                     82m
monitor-kube-state-metrics                ClusterIP   10.100.116.110   <none>        8080/TCP                     82m
monitor-prometheus-node-exporter          ClusterIP   10.97.172.225    <none>        9100/TCP                     82m
prometheus-operated                       ClusterIP   None             <none>        9090/TCP                     82m
```

可能需要暴露的服务有：`monitor-grafana`、`monitor-kube-prometheus-st-alertmanager`、`monitor-kube-prometheus-st-prometheus`

一些无数据的问题可以参考：[使用 Prometheus Operator 监控 Kubernetes](https://cloud.tencent.com/developer/article/1561748)

## 参考链接

[kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)

[failed calling webhook](https://github.com/helm/charts/issues/19928#issuecomment-596961503)

[StorageClass](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/user-guides/storage.md)

[prometheus book](https://yunlzheng.gitbook.io/prometheus-book/introduction)
