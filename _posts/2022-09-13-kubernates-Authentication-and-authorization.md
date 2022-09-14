---
layout: post
title: kubernetes的安全之认证与授权
categories: [kubernetes, cloud native, security]
description: kubernetes的安全之认证与授权
keywords: kubernetes, cloud native, security
---

kubernetes的安全之认证与授权
1. ### 认证
三种常见的认证方式:
 > kubectl与ApiServer进行双向TLS认证

 > BearerToken

 > ServiceAccount(namespace,token,ca)

![](/images/posts/cloud/2022-09-13-kubernetes-authentication.png)

2. ### 授权
授权机制:
> ABAC

> WebHook

> RBAC(Role Based Access Control):主要使用这种

![](/images/posts/cloud/2022-09-13-kubernetes-authorization.png)

3. ### AdmisionControl准入控制
几个代表性的插件
 >AlwaysAdmit 总是允许全部请求通过

 >AlwaysDeny 总是拒绝请求通过

 >ServiceAccount ServiceAccount自动化，确保ServiceAccount在每个POD都存在

 >DenyEscolatingExec 限制登录到我们的容器中执行命令的