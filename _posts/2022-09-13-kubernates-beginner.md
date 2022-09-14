---
layout: post
title: kubernetes入门
categories: [kubernetes, cloud native]
description: kubernetes入门
keywords: kubernetes, cloud native
---

kubernetes入门
1. ### kubernetes模型
![](/images/posts/cloud/2022-09-13-kubernetes-model.png)

2. ### kubernetes架构
    Master内部:
    ApiServer:调动kubernetes，外部与kubernetes交互的接口，操作k8s的入口 
    Scheduler: 收集workder的信息
    ControllerManager： 控制中心，维护k8s的对象，关注对象的状态

ETCD: 存储组件，持久化

Worker内部:
    Kubelet: 维护POD生命周期,包括网络，调用Docker实现运行容器，运行POD
    Docker
![](/images/posts/cloud/2022-09-13-kubernetes-struction.png)

