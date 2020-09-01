---
title: VMware 使用：虚拟机网络配置采坑记
date: 2020-09-01 23:41:35
categories: VMware
summary: VMware VirtualBox 虚拟机网络配置
tags:
    - VirtualBox
    - Centos
    - 虚拟机网络配置
---

## VMware 使用：虚拟机网络配置采坑记

### 1.0 遇到的问题

之前用 `VirtualBox` 安装了 `Centos` 虚拟机，但是一直连接不了网络，无法正常使用，问题主要是网络配置的问题；

虚拟的网络配置目前主要的有 NAT ，桥接等。看了网上很多的博客，都没能帮我解决联网问题；

报的错误有： 

1）ping www.baidu.com ping不通 ping 宿主机 也ping不通

2）无法获取 IP ， 执行 ip addr 查看不到网卡信息 也没有 ip 信息

3) 报错：Network is unreachable

4）报错：IP configuration could not be reserved

总之 就是虚拟机不能联网

经过多次实践，总算解决了，总结一下吧；

### 2.0 VMware 安装 Centos 后如何配置联网

#### 2.1 打开虚拟机设置，选择 NAT 模式
![虚拟机网络配置 选择 NAT](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-09-01/20200901-8.png)

#### 2.2 我安装的是 Centos `CentOS-7-x86_64-Everything-2003` 版本
问题可能和不同版本的系统也有关系，我安装的这个版本安装完后，没有 eth0 网卡配置，解决方式如下：

第一步：创建 `ifcfg-eth0 ` 配置文件

```
cd /etc/sysconfig/network-scripts/
mv ifcfg-ens33 ifcfg-eth0 
```

第二步：编辑 `ifcfg-eth0 `

```
vi ifcfg-eth0 
```

内容如下：

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID=e7eaefa6-0bd5-417c-a4ac-27e3a4a29e56
DEVICE=eth0
ONBOOT=yes
NM_CONTROLLED=yes
```

第三步：配置信息（这一步不知道是不是必须，我反正是做了，也可能不用也可以）

编辑/etc/default/grub文件，在GRUB_CMD_LINE_LINUX=""项中，插入"net.ifnames=0 biosdevname=0"（与原有项之间用空格隔开），保存退出。

调用命令grub2-mkconfig -o /boot/grub2/grub.cfg更新配置，然后直接reboot重启

目前虚拟机已经可以正常联网，宿主机也可以通过 `ssh` 正常连接到虚拟机，互相都可以 `ping` 通；
![ssh 访问正常](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-09-01/20200901-9.png)
