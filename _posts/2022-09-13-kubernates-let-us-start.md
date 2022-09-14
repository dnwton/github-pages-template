---
layout: post
title: kubernetes安装
categories: [kubernetes, cloud native]
description: kubernetes安装
keywords: kubernetes, cloud native
---

## kubernetes安装方式对比
1. ### Kubeadm
![](/images/posts/cloud/2022-09-13-kubernetes-start-kubeadm.png)

2. ### Binary
![](/images/posts/cloud/2022-09-13-kubernetes-start-binary.png)

## 服务器环境准备
1. 修改主机名
hostname set-hostname < yourhostname >
2. vi修改各主机/etc/hosts

如:
> 192.168.0.* m1

> 192.168.0.* m2

> 192.168.0.* m3

> 192.168.0.* s1

> 192.168.0.* s2

使通过主机名可以连通个主机

3. 更新安装依赖包

更新yum
```
$ yum update
```
安装依赖包
```
$ yum install -y conntrack ipvsadm ipset jq sysstat curl iptables libseccomp
```
4. 关闭放防火墙,swap,重置iptables

关闭防火墙
```
$ systemctl stop firewalld && systemctl disable firewalld
```
重置iptables
```
$ iptables -F && iptables -X && iptables -F -t nat && iptables -X -t nat && iptables -P FORWARD ACCEPT
```
关闭swap(影响性能)
```
$ swapoff -a
```
```
$ sed -i '/swap/s/^\(.*\)$/#\1/g' /etc/fstab
```
关闭selinux
```
$ setenforce 0
```
关闭dnsmasq(否则可能导致docker容器无法解析域名)
```
$ service dnsmasq stop && systemctl disable dnsmasq
```

5. 系统参数设置

### 制作kubernetes配置文件
```
$ cat > /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
net.ipv4.ip_forward=1
vm.swappiness=0
vm.overcommit_memory=1
vm.panic_on_oom=0
fs.inotify.max_user_watches=89100
EOF

```
### 生效文件
```
$ sysctl -p /etc/sysctl.d/kubernetes.conf
```
### 报错
> sysctl: cannot stat /proc/sys/net/bridge/bridge-nf-call-iptables: No such file or directory

> sysctl: cannot stat /proc/sys/net/bridge/bridge-nf-call-ip6tables: No such file or directory

### 报错解决
```
$ modprobe br_netfilter
```

6. 安装docker
```
$ mkdir -p /opt/kubernetes/docker && cd /opt/kubernetes/docker
$ wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-17.03.1.ce-1.el7.centos.x86_64.rpm
$ wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-selinux-17.03.1.ce-1.el7.centos.noarch.rpm
$ wget https://download.docker.com/linux/centos/7/debug-x86_64/stable/Packages/docker-ce-debuginfo-17.03.1.ce-1.el7.centos.x86_64.rpm
```
### 清理原有版本
```
$ yum remove -y docker* container-selinux
```
### 安装rpm包
```
$ yum localinstall -y *.rpm
```
### 开机启动
```
$ systemctl enable docker
```
## 设置参数
### 查看磁盘挂载
```
$ df -h
```
### 设置docker启动参数
- 设置docker数据目录:选择比较大的分区("graph":"/docker/data/path"我这里是根目录就不需要配置了,默认为/var/lib/docker)
- 设置cgroup driver

```
$ cat <<EOF > /etc/docker/daemon.json
{
    "graph":"/docker/data/path",
    "exec-opts":["native.cgroupdriver=systemd"]
}
EOF
```
### 启动docker服务
```
service docker restart
```

7. 安装必要的工具

- kubeadm:部署集群用的命令
- kubelet:在集群中要运行的组件，负责管理POD，容器的生命周期
- kubectl:集群管理工具(在控制集群的节点安装即可)

> 安装方法

配置yum源(机器可连外网的可将mirrors.aliyun.com换成packages.cloud.google.com)
```
$ cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```
#### 安装工具
#### 找到要安装的版本号
```
yum list kubeadm --showduplicates | sort -r
```
#### 指定安装版本(这里用的是1.114.0)
```
yum install -y kubeadm-1.14.0-0 && kubelet-1.14.0-0 && kubectl-1.14.0-0 --disableexcludes=kubernetes
```

启动kubelet
```
systemctl enable kubelet && systemctl start kubelet
```

linux查看日志
```
journalctl -f
```

8. 准备配置文件
