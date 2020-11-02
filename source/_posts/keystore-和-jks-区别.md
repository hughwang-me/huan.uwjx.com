---
title: keystore 和 jks 区别
date: 2020-11-02 14:51:45
categories: Keystore和JKS 
summary: keystore 和 jks 区别
tags:
    - Keystore
    - JKS
    - Android签名
---

## keystore 和 jks 区别

### 1. 区别

`keystore` 是 `Eclipse` 生成的签名文件；

`JKS` 是 `Android studio` 生成的签名文件；

二者功能没啥大区别，可以转换；

### 2. `JKS` 转 `keystore`

#### 2.1 `keystore` 生成
```
keytool -genkey -alias [alias] -keyalg RSA -keysize 2048 -validity 36500 -keystore [name.keystore]
```
`-alias` 别名

`-keyalg` 签名算法

`-keysize` key大小

`-validity` 有效期限

`-keystore` keystore名称

示例:
```
keytool -genkey -alias shanhengKeystore -keyalg RSA -keysize 2048 -validity 36500 -keystore shanheng.keystore
```
说明：运行 `keytool` 命令生成keystore 需要jdk环境

#### 2.2 查看keystore信息
`keytool -list -v -keystore [name.keystore]`

返回示例：
```
$ keytool -list -v -keystore shanheng.keystore
输入密钥库口令:  SH@2020
密钥库类型: jks
密钥库提供方: SUN

您的密钥库包含 1 个条目

别名: shanhengkeystore
创建日期: 2020-11-2
条目类型: PrivateKeyEntry
证书链长度: 1
证书[1]:
所有者: CN=SH, OU=SH, O=SH, L=CN, ST=CN, C=CN
发布者: CN=SH, OU=SH, O=SH, L=CN, ST=CN, C=CN
序列号: 1d37dd33
有效期为 Mon Nov 02 15:09:39 CST 2020 至 Wed Oct 09 15:09:39 CST 2120
证书指纹:
         MD5:  29:3E:8A:14:7E:F0:97:F6:50:DA:BF:D6:3A:6E:AD:F8
         SHA1: EB:AB:C3:33:6A:9B:56:39:52:72:25:05:AE:68:73:99:78:9E:EE:F9
         SHA256: 52:74:44:22:1B:DB:35:01:62:55:EC:99:2A:B7:ED:1C:B8:81:37:FB:17:6E:02:6B:C3:71:47:5A:BB:CE:53:9B
签名算法名称: SHA256withRSA
主体公共密钥算法: 2048 位 RSA 密钥
版本: 3

扩展:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 0A 8E 02 DB E7 2F 0E C8   D2 23 96 83 10 B9 97 CC  ...../...#......
0010: F2 25 EB 8E                                        .%..
]
]



*******************************************
*******************************************



Warning:
JKS 密钥库使用专用格式。建议使用 "keytool -importkeystore -srckeystore shanheng.keystore -destkeystore shanheng.keystore -deststoretype pkcs12" 迁移到行业标准格式 PKCS12。
```

#### 2.3 秘钥库类型
`keytool -importkeystore -srckeystore shanheng.keystore -destkeystore shanheng.keystore -deststoretype pkcs12`

可以将秘钥库类型由 `jks` 转换成 `PKCS12`

转换完后执行 `keytool -list ...` 查看秘钥信息如下：

```
$ keytool -list -v -keystore shanheng.keystore
输入密钥库口令:  SH@2020
密钥库类型: PKCS12
密钥库提供方: SUN

您的密钥库包含 1 个条目

别名: shanhengkeystore
创建日期: 2020-11-2
条目类型: PrivateKeyEntry
证书链长度: 1
证书[1]:
所有者: CN=SH, OU=SH, O=SH, L=CN, ST=CN, C=CN
发布者: CN=SH, OU=SH, O=SH, L=CN, ST=CN, C=CN
序列号: 1d37dd33
有效期为 Mon Nov 02 15:09:39 CST 2020 至 Wed Oct 09 15:09:39 CST 2120
证书指纹:
         MD5:  29:3E:8A:14:7E:F0:97:F6:50:DA:BF:D6:3A:6E:AD:F8
         SHA1: EB:AB:C3:33:6A:9B:56:39:52:72:25:05:AE:68:73:99:78:9E:EE:F9
         SHA256: 52:74:44:22:1B:DB:35:01:62:55:EC:99:2A:B7:ED:1C:B8:81:37:FB:17:6E:02:6B:C3:71:47:5A:BB:CE:53:9B
签名算法名称: SHA256withRSA
主体公共密钥算法: 2048 位 RSA 密钥
版本: 3

扩展:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 0A 8E 02 DB E7 2F 0E C8   D2 23 96 83 10 B9 97 CC  ...../...#......
0010: F2 25 EB 8E                                        .%..
]
]



*******************************************
*******************************************
```

转换完之后，除了 `秘钥库类型` 其余都不变

#### 2.4 生成 `jks` 文件

直接通过 `Android Studio` 生成 .jks 文件

默认生成的秘钥库类型是 `jks` 信息如下：
```
$ keytool -list -keystore shanheng.jks
输入密钥库口令:  SH@2020
密钥库类型: jks
密钥库提供方: SUN

您的密钥库包含 1 个条目

shanheng, 2020-11-2, PrivateKeyEntry,
证书指纹 (SHA1): 08:E5:F0:0F:C3:5E:07:18:AB:65:6A:2A:6D:1D:61:73:31:70:BA:E6

Warning:
JKS 密钥库使用专用格式。建议使用 "keytool -importkeystore -srckeystore shanheng.jks -destkeystore shanheng.jks -deststoretype pkcs12" 迁移到行业标准格式 PKCS12。
```

转换秘钥库类型
```
$ keytool -importkeystore -srckeystore shanheng.jks -destkeystore shanheng.jks -deststoretype pkcs12
输入源密钥库口令:  SH@2020
已成功导入别名 shanheng 的条目。
已完成导入命令: 1 个条目成功导入, 0 个条目失败或取消

Warning:
已将 "shanheng.jks" 迁移到 Non JKS/JCEKS。将 JKS 密钥库作为 "shanheng.jks.old" 进行了备份。
```

转换后的信息如下：
```
$ keytool -list -keystore shanheng.jks
输入密钥库口令:  SH@2020
密钥库类型: PKCS12
密钥库提供方: SUN

您的密钥库包含 1 个条目

shanheng, 2020-11-2, PrivateKeyEntry,
证书指纹 (SHA1): 08:E5:F0:0F:C3:5E:07:18:AB:65:6A:2A:6D:1D:61:73:31:70:BA:E6
```
#### 2.5 将 jks 转换成 keystore

先生成 `p12`文件
`keytool -importkeystore -srckeystore shanheng.jks -srcstoretype JKS -deststoretype PKCS12 -destkeystore shanheng.p12`

```
$ keytool -importkeystore -srckeystore shanheng.jks -srcstoretype JKS -deststoretype PKCS12 -destkeystore shanheng.p12
正在将密钥库 shanheng.jks 导入到 shanheng.p12...
输入目标密钥库口令:  SH@2020
再次输入新口令: SH@2020
输入源密钥库口令:  SH@2020
已成功导入别名 shanheng 的条目。
已完成导入命令: 1 个条目成功导入, 0 个条目失败或取消
```



将 `p12`文件转换成 `keystore`
`keytool -v -importkeystore -srckeystore shanheng.p12 -srcstoretype PKCS12 -destkeystore shanheng2.keystore -deststoretype JKS`

```
$ keytool -v -importkeystore -srckeystore shanheng.p12 -srcstoretype PKCS12 -destkeystore shanheng2.keystore -deststoretype JKS
正在将密钥库 shanheng.p12 导入到 shanheng2.keystore...
输入目标密钥库口令:  SH@2020
再次输入新口令: SH@2020
输入源密钥库口令:  SH@2020
已成功导入别名 shanheng 的条目。
已完成导入命令: 1 个条目成功导入, 0 个条目失败或取消
[正在存储shanheng2.keystore]

Warning:
JKS 密钥库使用专用格式。建议使用 "keytool -importkeystore -srckeystore shanheng2.keystore -destkeystore shanheng2.keystore -deststoretype pkcs12" 迁移到行业标准格式 PKCS12。
```

转换后的 keystore信息：
```
$ keytool -list -keystore shanheng2.keystore
输入密钥库口令:  SH@2020
密钥库类型: PKCS12
密钥库提供方: SUN

您的密钥库包含 1 个条目

shanheng, 2020-11-2, PrivateKeyEntry,
证书指纹 (SHA1): 08:E5:F0:0F:C3:5E:07:18:AB:65:6A:2A:6D:1D:61:73:31:70:BA:E6

```

可以看出 转换后的 SHA1 完全一样
