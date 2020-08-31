---
title: Andorid 打多渠道包方法
date: 2020-08-31 08:43:56
categories: Android打包
summary: Android 多渠道打包方法
tags:
    - Android
    - 渠道包
---

## Android 多渠道打包

### 1.0 动态配置属性

 在 AndroidManifest.xml 文件中定义 

```
<meta-data android:value="${channel}" android:name="KEY_NAME"/>
``` 

其中${channel} 就是定义了将来可以在打包过程中动态配置的属性，配置方式是在 build.gradle 中进行处理。

### 2.0 配置 build.gradle

#### 2.1 在 defaultConfig 中加入如下配置，指明要动态配置的维度。
```
flavorDimensions "channel"
```

#### 2.2 在 android 下配置渠道属性信息
```
productFlavors {
        baidu {
            dimension "channel"
            manifestPlaceholders = [channel: "baidu"]
        }
        xiaomi {
            dimension "channel"
            manifestPlaceholders = [channel: "xiaomi"]
        }
        qihu360 {
            dimension "channel"
            manifestPlaceholders = [channel: "qihu360"]
        }
        yingyongbao {
            dimension "channel"
            manifestPlaceholders = [channel: "yingyongbao"]
        }
        huawei {
            dimension "channel"
            manifestPlaceholders = [channel: "huawei"]
        }
    }
```
 
#### 2.3 执行打包指令。
``` bash
$ ./gradlew clean assembleRelease
```

### 3.0 打包结果
![打包后生成的文件夹](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/20200831-1.png)
 
 ![打包后文件夹内文件列表](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/20200831-2.png)

 
