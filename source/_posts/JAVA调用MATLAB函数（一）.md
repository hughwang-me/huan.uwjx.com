---
title: JAVA调用MATLAB函数（一）
date: 2020-09-21 17:50:03
categories: MATLAB 
summary: JAVA调用MATLAB函数（一）
tags:
    - MATLAB
    - JAVA调用MATLAB
    - MATLAB打jar包
---

## JAVA调用MATLAB函数（一）

### 1.安装 MATLAB 和 注册破解

#### 1.1 下载 MATLAB 和破解文件：
```
链接: https://pan.baidu.com/s/1-MkTfIjFKvBCFNZ782vQhA 提取码: jr6j 
链接失效可以联系 hugh#uwjx.com
```

#### 1.2 安装 MATLAB安装和破解

将 ISO 文件加载到虚拟光驱，点击安装；
安装过程中选择“我已有我的许可证的文件安装密钥”，在下面输入秘钥 `09806-07443-53955-64350-21751-41297` ，点击下一步。

下载好的破解文件“Matlab 2016a Win64 Crack.rar”解压缩。解压后的“license_standalone.lic”就是许可文件;

打开 Matlab 的安装目录 …R2016a\bin\win64（如 D:\Matlab _R2016a\R2016a\bin\win64）。点击 “ activate _matlab.exe”进行激活。

选择“在不选择 Internet情况下手动激活”，点击“下一步”继续。

选择“输入许可证文件的完成路径（包括文件名）”。点击“浏览”，选择刚才解压到桌面的 Matlab 2016a Win64 Crack 文件夹中的许可文件“license_standalone.lic”，点击选择。再点击下一步。

显示激活已完成。点击完成。注意，此时还没有激活完成，不要打开软件；

打开解压的 Matlab 2016a Win64 Crack 文件夹中的 MATLAB Production Server\R2016a\bin\win64 文件夹，复制其中的 .dll 文件。

```
cufft64_75.dll
instutil.dll
libmwservices.dll
pst.dll
```

之后打开 Matlab 安装目录 …R2016a\bin\win64（比如 D:\Matlab _R2016a\R2016a\bin\win64），将复制的dll 文件粘贴在这个文件夹中，系统会提示“复制文件”选项，所有文件都选择“复制和替换”即可。（如果像我一样将安装目录直接和我们下载的安装包放在一起，则解压缩破解文件的时候，就会发生替换操作）

至此破解完成。

### 2.配置 MATLAB

#### 2.1 保持本机的 java 版本和 MATLAB 的 java 版本一致（必须）

本机使用 java -version 查看版本
MATLAB 在命令行窗口执行 `version -java` 查看 MATLAB 的java版本 必须一致

#### 2.2 jdk各种老版本的下载链接
```
java SE 1.6各个版本 jdk  api

http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase6-419409.html

java SE 1.7各个版本 jdk  api

http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html

java SE 1.8各个版本 jdk  api

http://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase8-2177648.html
```

### 3.安装 `MCR` 
必须要安装 MCR 不然打好的 jar 包在java里面执行不了！！！

首先在 MATLAB 的命令窗口执行 `mcrinstaller` 命令查看是否已经安装，如果没有安装会看到安装文件在哪里，直接进去安装就可以；

安装完成MCR后需要配置环境变量

比如我的是：
```
F:\Program Files\MATLAB\MATLAB Runtime\v901\runtime\win64
```
添加到 PATH 就可以
