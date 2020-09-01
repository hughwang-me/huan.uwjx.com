---
title: VirtualBox 使用（一） 安装和创建运行 Centos 系统
date: 2020-09-01 10:26:26
categories: VirtualBox
summary: VirtualBox 安装和创建运行 Centos 系统
tags:
    - VirtualBox
    - Centos
---

## VirtualBox 使用（一） 安装和创建运行 Centos 系统

### 1.0 目标完成的任务

 在 Windows 或者 Mac 上运行 Centos 系统 ，执行连接，并在 Centos 上完成开发等工作；

### 2.0 准备必要的材料

> 下载和安装 VirtualBoax : https://www.virtualbox.org

> 下载 Centos 8.2 系统镜像文件 : https://www.centos.org/download

### 3.0 安装 VirtualBox 并创建虚拟磁盘和注册 Centos 系统镜像文件

#### 3.1 安装 VirtualBox

直接点击安装程序进行安装即可，安装完成后的界面：
![安装完成 VirtualBox](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-09-01/20200901-1.png)

#### 3.2 创建虚拟磁盘 用来安装系统和使用等
在工具中选中介质管理，进行创建，选中相关属性即可：
![工具](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-09-01/20200901-2.png)
![创建介质](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-09-01/20200901-3.png)

#### 3.3 注册 Centos 或者其他系统镜像文件
下载后的系统镜像，需要注册后才可以使用；
![注册系统镜像](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-09-01/20200901-4.png)

### 4.0 创建和运行 Centos 系统

#### 4.1 创建 Centos 虚拟机
在工具中创建新的虚拟机，填写相关属性，关联上面创建的磁盘和注册的系统镜像
![创建虚拟机](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-09-01/20200901-5.png)

#### 4.2 启动虚拟机，安装 Centos 系统
![安装 Centos 系统](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-09-01/20200901-6.png)
安装过程中可能需要选择安装的磁盘，选择之前创建的虚拟磁盘就可以，创建 Root 密码；

### 5.0 完成安装，登录虚拟机
![安装完成](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-09-01/20200901-7.png)
 
