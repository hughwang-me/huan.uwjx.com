---
title: Spring AOP 编程(一)
date: 2020-12-09 00:21:53
summary: Spring AOP 编程(一)
tags:
    - Spring AOP
    - AOP
---

## Spring AOP 编程(一)

```
本章示例源码：https://github.com/hughwang-me/hugh-java-examples/tree/master/aop-testing
```

### 第一步：引入依赖
```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
```

### 第二步：PointCut Expression

#### 第一种：designators : execution()
```
@Aspect
@Component
public class DesignatorsPointCut {

    @Pointcut("execution(* com.uwjx.aoptesting.service.TestService.*(..))")
    public void pointCut(){

    }
}
```

#### 第二种：wildcards : * .. +
```
@Before("execution(* com.savage.aop.MessageSender.*(..))")
```

#### 第三种：operators : && || ! 
```
@Pointcut("logSender() || logReceiver()")
private void logMessage(){}

```
### 第三步：五种 Advice 注解

#### 第一种： `@Before`
#### 第二种： `@After`
#### 第三种： `@AfterReturning`
#### 第四种： `@AfterThrowing`
#### 第五种： `@Around`

