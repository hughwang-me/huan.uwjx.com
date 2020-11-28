---
title: Docker使用(二) 更换 Docker Root Dir
date: 2020-11-28 14:35:07
categories: Docker使用
summary: Docker使用(二) 更换 Docker Root Dir
tags:
    - Docker
    - Docker Root Dir
---

## Docker使用(二) 更换 Docker Root Dir

### 1.0 通过 `docker info` 查看当前的 Docker Root Dir

```
[root@iZuf65f969nipfghw5ym2nZ ~]# docker info
Client:
 Debug Mode: false

Server:
 Containers: 23
  Running: 7
  Paused: 0
  Stopped: 16
 Images: 23
 Server Version: 19.03.13
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 8fba4e9a7d01810a393d5d25a3621dc101981175
 runc version: dc9208a3303feef5b3839f4323d9beb36df0a9dd
 init version: fec3683
 Security Options:
  seccomp
   Profile: default
 Kernel Version: 3.10.0-1127.19.1.el7.x86_64
 Operating System: CentOS Linux 7 (Core)
 OSType: linux
 Architecture: x86_64
 CPUs: 2
 Total Memory: 15.51GiB
 Name: iZuf65f969nipfghw5ym2nZ
 ID: KZSY:EJKH:IBHR:TLWU:QT5X:OMEB:CX35:425D:DSFB:L2WU:G3YL:IOAA
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Registry Mirrors:
  https://xxx.mirror.aliyuncs.com/
 Live Restore Enabled: false
```

可以看到 默认的 Docker Root Dir 是 `/var/lib/docker`

```
    ...
    Docker Root Dir: /var/lib/docker
    ...
```

### 2.0 方式一：通过修改 `daemon.json` 方式修改

#### 2.1 修改 `/etc/docker/daemon.json`

```
    vim /etc/docker/daemon.json
```
#### 2.2 添加  `graph` 属性到你想要的 docker root dir 路径 ：
```
{
  // ...
  "graph": "/mnt/disk50/docker-root"
 // ...	
}
```

#### 2.3 执行重新加载和重启
```
systemctl daemon-reload
systemctl restart docker //重启

systemctl start docker  //获取已经停止的话 直接启动
```

### 2.4 查看修改后的 `docker info`

```
...
 ID: KZSY:EJKH:IBHR:TLWU:QT5X:OMEB:CX35:425D:DSFB:L2WU:G3YL:IOAA
 Docker Root Dir: /mnt/disk50/docker-root
 Debug Mode: false
...
```

可以看到 已经切换了

 
### 3.0 方式二：通过建立软链接方式

因为 Docker 默认的 Docker Root Dir 是 `/var/lib/docker`

 所以只要将目标路径链接到默认路径就可以
 
```
    ln -s /var/lib/docker /opt/docker
```

### 4.0 方式三：通过临时配置，重启或者重新加载就会失效
```

```
