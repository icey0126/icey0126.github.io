---
title: K8S学习笔记
date: 2021-10-26 23:06:32
tags: 
- k8s
categories: 
- CS
---

前阵子学了点杨波老师的k8s的入门课程，感觉讲得很清楚。最近公司内部正好在推GitOps，需要自己写一些yaml文件。干脆整理下之前学的东西。

其实看日期也能知道是10月创建的文章，硬是被我拖延到了今天才写。

下面可能包含很多本人错误的理解，容我先免个责。

以及墙裂建议看过杨波老师的入门课再去看官网文档，很多概念理解起来会快很多。指个路：
[Kubernetes基本概念和应用](https://www.bilibili.com/video/BV1Ja4y1x748) 也就3个小时左右，~~两倍速更快~~

1.Pod
> - Pod 类似于共享名字空间和文件系统卷的一组 Docker 容器。

比较常见的是每个Pod一个容器，也会有一个Pod多个容器的情况。
当一个Pod有多个容器时，它的成员容器共享网络和存储。

一个简单的Pod资源定义文件：

```bash

apiVersion: v1 #使用版本
kind: Pod #类型
metadata: #元数据
  name: myweb #资源名字，在同一个namespace下必须唯一
  labels:
    name: myweb #标签
spec:
  containers:
  - name: myweb
    image: kubeguide/tomcat-app: v1
    ports:
    - containerPort: 8080
    env:
    - name: MYSQL_SERVICE_HOST
      value: 'mysql'
    - name: MYSQL_SERVICE_PORT
      value: '3306'

```

写不动了，哪天心情好了再继续。

2.NodePort 

3.Service

4.ReplicaSet

5.Deployment

6.ClusterIP Service

7.ConfigMap

8.Ingress

9.Volume
