---
title: Jenkins 部署和使用（一）
date: 2020-09-22 23:19:36
categories: Jenkins 
summary: Jenkins 部署和使用（一）
tags:
    - Jenkins
    - Jenkins部署
    - Jenkins使用
---

## Jenkins 部署和使用（一）

### 1. 使用 Docker 方式部署 Jenkins
本项目源码
```
https://github.com/hughwang-me/jenkin-component
```
文档地址
```
https://www.jenkins.io/zh/doc/book/installing/#%E5%9C%A8docker%E4%B8%AD%E4%B8%8B%E8%BD%BD%E5%B9%B6%E8%BF%90%E8%A1%8Cjenkins
```

使用如下命令下载并运行 Jenkins 容器
```
docker run -u root --rm -d -p 9010:8080 -p 9011:50000 -v jenkins-data:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean
```

完成之后web浏览器打开 `ip:9010` 完成 jenkins 初始化配置；

### 2. 节点配置

新增节点 填写IP等信息
注意 java位置的配置 在 高级 里面
```
/root/jdk1.8.0_201/bin/java
```
