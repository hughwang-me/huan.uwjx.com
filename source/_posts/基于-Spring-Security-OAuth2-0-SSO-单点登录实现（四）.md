---
title: 基于 Spring-Security-OAuth2.0 SSO 单点登录实现（四）
date: 2020-09-03 20:54:34
categories: Spring Security OAuth2.0
summary: 单点登录实现
tags:
    - Spring Security
    - Spring OAuth2.0
    - SSO 单点登录
---

## 基于 Spring Security OAuth2.0 SSO 单点登录实现（四）

### 拦截器优先级问题

请求优先由@EnableAuthorizationServer、@EnableResourceServer 处理，剩下的无法匹配的由SpringSecurity处理。

### 导致的问题

如果在同一个服务中启动这3个注解，会导致 `oauth/authorize` 被 `@EnableResourceServer` 拦截，
