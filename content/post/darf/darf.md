---
title: "一张草稿纸"
date: 2020-03-01T23:07:56+08:00
tags: ["none",]
draft: true 

---

# 

```yaml
apiVersion: v1
kind: Repository
metadata:
  name: "gorda.io"
spec:
  selector:
    org: "gorda"
    repository: "gorda.io"
  maintainer:
  - "aaa"
  - "bbb"
  - "ccc"
  token: "xxx"
---
apiVersion: v1
kind: Issue
metadata:
  name: "gorda.io-issue-create"
spec:
  selector:
    org: "gorda"
    repository: "gorda.io"
  title: "-"
  labels:
  - "status/welcome"
  - "kind/page"
  - "release/v0.3"
  milestone: 1     
---
apiVersion: v1
kind: IssueComment
metadata:
  name: "gorda.io-issue-confirm"
spec:
  selector:
    org: "gorda"
    repository: "gorda.io"
  labels:
  - "status/pending"
  projects:
    id: 9527
    column: 10010
state:
  instruct: "confirm"
  permision:
  - "maintainer"
  labels:
  - "status/welcome"
---
apiVersion: v1
kind: IssueComment
metadata:
  name: "gorda.io-issue-accept"
spec:
  selector:
    org: "gorda"
    repository: "gorda.io"
  labels:
  - "status/waiting-for-pr"
  limit: 10
  assignees: "commentator"
  success: "thank you ..."
  fail: "sorry ..."
  projects:
    id: 9527
    column: 10086
state:
  instruct: "accept"
  permision: "member"
  assignees: "noone"
  labels:
  - "status/pending"
  projects:
    id: 9527
    column: 10010
---
apiVersion: v1
kind: IssueComment
metadata:
  name: "gorda.io-issue-pushed"
spec:
  selector:
    org: "gorda"
    repository: "gorda.io"
  label:
  - "status/reviewing"
  success: "after the pr merged, you can..."
  fail: "sorry ..."
state:
  instruct: "pushed"
  permision: 
  - "maintainer"
  - "commentator"
  assignees: 
  - "commentator"
  labels:
  - "status/waiting-for-pr"
---
apiVersion: v1
kind: IssueComment
metadata:
  name: "gorda.io-issue-merged"
spec:
  selector:
    org: "gorda"
    repository: "gorda.io"
  labels:
  - "status/finished"
  state: "close"
state:
  instruct: "merged"
  permision: 
  - "maintainer"
  - "commentator"
  assignees: 
  - "commentator"
  labels:
  - "status/waiting-for-pr"
---
apiVersion: v1
kind: PullRequest
metadata:
  name: "gorda.io-pull-request-create"
spec:
  selector:
    org: "gorda"
    repository: "gorda.io"
  labels:
state:
  instruct: "accept"
---
apiVersion: v1
kind: PullRequestComment
metadata:
  name: "gorda.io-pull-request-cc"
spec:
  selector:
    org: "gorda"
    repository: "gorda.io"
  reviews:
  - "mention-in-cc"
state:
  instruct: "cc"
---
apiVersion: v1
kind: PullRequestComment
metadata:
  name: "gorda.io-pull-request-assign"
spec:
  selector:
    org: "gorda"
    repository: "gorda.io"
  assignes:
  - "mention-in-assign"
state:
  instruct: "assign"
  permision:
  - "maintainer"
```

# cp -f 依然提示是否覆盖

[linux cp拷贝覆盖不提示](https://blog.csdn.net/sunny05296/article/details/78607277)

# 网页指定 favicon

[设置favicon的几种方式](https://blog.csdn.net/YLXB2/article/details/53336962)

# 路线流程

- 安装 proxmox 
- 基于 cloud-init 快速初始化多个节点
- 基于 [kubespray](https://github.com/kubernetes-sigs/kubespray#requirements) 快速安装安装 k8s 高可用集群

# 还不知道怎么弄的下一步

- 快速安装 Istio
- 自动部署项目
- 快速安装一堆组件，监控，可视化，日志等

# 需要持久化的内容

- 各种数据（数据库/缓存/网络文件系统等）
- 各种文件（软件包/镜像等），以加快二次下载速度（是否可行？）
- 项目内容（代码/配置/环境变量/脚本等），放在代码仓库

# 目标

全流程自动化（节点 -> k8s 集群 -> Istio -> 项目）

# 问题记录

- 未订阅的情况下，proxmox 每个虚拟机只能有一个备份

# 博客分类

日志（主页）
系列文章
Tags
实验室
杂文
关于我


# 博客风格

目前有什么问题/需求
xxx 能实现 & 怎么用
xxx 是什么 & 大概原理
xxx 还能做些什么
参考链接
