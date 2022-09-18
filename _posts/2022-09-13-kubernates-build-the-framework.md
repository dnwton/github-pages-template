---
layout: post
title: kubernetes安装之搭建高可用
categories: [kubernetes, cloud native, HAProxy, keepalived]
description: kubernetes安装
keywords: kubernetes, cloud native, HAProxy, keepalived
---

## 基于kubeadm部署kubernates1.25 .ver高可用集群
1. ### 部署kubernetes集群API访问入口高可用(这里用两台机器来做)
- 安装HAProxy
> 利用HAProxy实现kubeapi服务的负载均衡

```
$ cat >> /etc/sysctl.conf <<EOF
net.ipv4.ip_nonlocal_bind = 1
EOF
```
```
$ sysctl -p
```

- 更新yum
```
$ yum update
$ yum install -y haproxy
```
#### 允许Linux进程绑定到Linux系统或服务器上尚不存在的IP地址,它允许进程将bind()绑定到非本地IP地址，这对于诸如Nginx，HAProxy，keepalived等负载平衡器的应用程序非常有用。
- 给haproxy.cfg添加以下内容
```
$ cat >> /etc/haproxy/haproxy.cfg <<EOF
listen stats # 状态页
    mode http
    bind 0.0.0.0:8888
    stats enable
    log global
    stats uri /status #登录访问status
    stats auth admin:123456

listen kubernetes-api-6443  # 
    bind 192.168.0.117:6443
    mode tcp
    server m1 192.168.0.110:6443 check inter 3s fall 3 rise 3
    server m2 192.168.0.109:6443 check inter 3s fall 3 rise 3
    server m3 192.168.0.108:6443 check inter 3s fall 3 rise 3
EOF
```

- 重启haproxy
```
systemctl restart haproxy
```
- 重启报错
```
'option httplog' not usable with proxy 
Starting proxy stats: cannot bind socket
```
- 可能是SELinux引发的安全问题，试一试下面的命令
```
$ setsebool -P haproxy_connect_any=1
```
Name
setsebool - set SELinux boolean value
Synopsis
setsebool [ -P ] boolean value | bool1=val1 bool2=val2 ...
Description
setsebool sets the current state of a particular SELinux boolean or a list of booleans to a given value. The value may be 1 or true or on to enable the boolean, or 0 or false or off to disable it.
Without the -P option, only the current boolean value is affected; the boot-time default settings are not changed.
If the -P option is given, all pending values are written to the policy file on disk. So they will be persistant across reboots.

2. 安装 keepalived

- 更新yum
```
$ yum update
```
```
安装 keepalived
$ yum install -y keepalived
```
- 创建keepalived配置文件
```
$ vim /etc/keepalived/keepalived.conf
! Configuration File for keepalived

global_defs {
    router_id ha1 # 指定router_id,在ha2上为ha2
}

vrrp_script check_haproxy { #定义脚本
    script "/etc/keepalived/check_haproxy.sh"
    interval 1
    weight -30
    fall 3
    rise 2
    timeout 2
}

vrrp_instance VI_1 {
    state MASTER #在ha2上为BACKUP
    interface ens33
    garp_master_delay 10
    smtp_alert
    virtual_router_id 66 #指定虚拟路由器ID,ha1和ha2此值必须相同
    priority 100 #在ha2上为80
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 123456 # 指定验证密码，ha1和ha2此值必须相同
    }
    virtual_ipaddress {
        192.168.0.145/24 dev ens33 label ens33:1 # 指定VIP，ha1和ha2此值必须相同，此IP为同网段的没有使用的虚拟IP
    }
    track_script {
        check_haproxy
    }
}

```

- 几个vim命令
```
$ u

$ ctrl+r

$ d$

$ dw

$ x

$ dd
$ v shift+G x

```
- 设置脚本进行健康性检测
```
$ cat > /etc/keepalived/check_haproxy.sh <<EOF
#!/bin/bash
/usr/bin/killall -0 haproxy || systemctl restart haproxy
EOF
```
- 赋予执行权限
```
$ chomd a+x /etc/keepalived/check_haproxy.sh
```
- 重启keepalived
```
systemctl restart keepalived
```
