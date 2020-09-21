---
title: JAVA调用MATLAB函数（二）
date: 2020-09-21 18:08:35
categories: MATLAB 
summary: JAVA调用MATLAB函数（二）
tags:
    - MATLAB
    - JAVA调用MATLAB
    - MATLAB打jar包
---

## JAVA调用MATLAB函数（二）

### 1.写一个简单的 MATLAB 测试函数

打开 MATLAB ，写入如下测试函数：
 
```
function [ sum,sub,mul,div ] = drawplot( a, b)
sum = a + b;
sub = a - b;
mul = a * b;
div = a / b;
end
```

### 2.打成 jar 包

在 MATLAB 执行 `deploytool` 命令，然后选择 `Library Compiler` 进入打 jar 包页面

TYPE 选择 `Java Package`

EXPORTED FUNCTIONS 选择上一步保存的.m文件

Library information 输入相关参数

Class Name 写入类名称，这个就是将来java里面调用的类名

配置好之后点击 Package 就可以打包了，得到一个jar文件

### 3.在java程序使用 jar包

需要引入2个jar包

1.我们自己打的jar，就是上一步生成的jar；

2.MATLAB 依赖包
```
F:\Program Files\MATLAB\R2016a\toolbox\javabuilder\jar 
下的 javabuilder.jar
``` 

编写java代码调用
```
import com.uwjx.matlab.MatlabTest; 
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.stereotype.Service;
 

@Service
@Slf4j
public class MatlabService implements InitializingBean {


    @Override
    public void afterPropertiesSet() throws Exception {
        log.warn("开始运行");

        int a = 9;
        int b = 3;
        Object[] result = null;
        //都怪我，这个类名和方法名起的太搓了……
        MatlabTest class1 = new MatlabTest();
        //虽然Matlab中只有2个输入量，但是查看生成的源码发现需要对结果对象的大小进行初始化
        // 这里表示有4个返回值数量总共有4个
        result = class1.drawplot(4, a, b);
        for(Object o:result) {
            log.warn(o.toString());
        }
    }

}

```

