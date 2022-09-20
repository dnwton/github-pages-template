---
layout: post
title: docker 映射配置文件到宿主机
categories: [docker]
description: docker 映射配置文件到宿主机
keywords: docker, 
---


1. ### docker 映射配置文件到宿主机(以mysql为例)
- mount方式：

> --mount type=bind,src=/mysql/conf/my.cnf,dst=/etc/my.cnf

- v方式

> -v /mysql/conf/my.cnf:/etc/my.cnf