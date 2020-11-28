---
title: Centos7 配置 NGINX 或者其他服务开机启动
date: 2020-11-28 14:41:56
categories: Centos Nginx
summary: Centos7 配置 NGINX 或者其他服务开机启动
tags:
    - Centos
    - Nginx
    - 开机启动
---

## Centos7 配置 NGINX 或者其他服务开机启动

#### 第一步：创建 `nginx.service` 文件

进入 `/lib/systemd/system` 目录，创建 `nginx.service` 文件

#### 第二步：编辑 `nginx.service` 文件

```
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
ExecStartPre=/usr/local/nginx/nginx -t 
ExecStart=/usr/local/nginx/nginx 
ExecReload=/usr/local/nginx/nginx -s reload
ExecStop=/usr/local/nginx/nginx -s quit
PrivateTmp=true
```

#### 第三步：测试和配制 `systemctl` 命令

1.配制开机启动

```
 systemctl enable nginx
```

2.启动或者关闭 `nginx` 服务

```
    systemctl start/stop nginx
```
