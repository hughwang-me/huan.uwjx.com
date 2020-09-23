---
title: Mysql使用（一）：新建数据库、用户配置访问权限
date: 2020-09-23 14:14:29
categories: Mysql 
summary: Mysql使用（一）：新建数据库、用户配置访问权限
tags:
    - Mysql
    - Mysql新建 
---

## Mysql使用（一）：新建数据库、用户配置访问权限

### 1. 新建数据库
```
--创建名称为“testdb”数据库，并设定编码集为utf8
CREATE DATABASE IF NOT EXISTS testdb DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
```

### 2. 新建用户
```
--创建了一个名为：test 密码为：1234 的用户
 create user 'test'@'localhost' identified by '1234';

注意：
此处的"localhost"，是指该用户只能在本地登录，不能在另外一台机器上远程登录。如果想远程登录的话，将"localhost"改为"%"，表示在任何一台电脑上都可以登录。也可以指定某台机器可以远程登录。
```

### 3. 更新密码
```
--方法1，密码实时更新；修改用户“test”的密码为“1122”
set password for test =password('1122');
--方法2，需要刷新；修改用户“test”的密码为“1234”
update  mysql.user set  password=password('1234')  where user='test'
--刷新
flush privileges;
```

### 4. 权限分配
```
--授予用户test通过外网IP对数据库“testdb”的全部权限
grant all privileges on 'testdb'.* to 'test'@'%' identified by '1234';  

--刷新权限
flush privileges; 

--授予用户“test”通过外网IP对于该数据库“testdb”中表的创建、修改、删除权限,以及表数据的增删查改权限
grant create,alter,drop,select,insert,update,delete on testdb.* to test@'%';	 
```

### 5. 查询用户权限
```
--查看用户“test”
show grants for test;
```
