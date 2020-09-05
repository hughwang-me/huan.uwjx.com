---
title: 基于 Spring Security OAuth2.0 SSO 单点登录实现（一）
date: 2020-08-30 15:31:55
categories: Spring Security OAuth2.0
summary: 单点登录实现
tags:
    - Spring Security
    - Spring OAuth2.0
    - SSO 单点登录
---

## 基于 Spring Security OAuth2.0 SSO 单点登录实现（一）

### 什么是单点登录

 

### 引入依赖
```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.security.oauth</groupId>
            <artifactId>spring-security-oauth2</artifactId>
            <version>2.3.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
```

### 创建 SpringSecurity 配制类 WebSecurityConfiguration
```
@Slf4j
@Component
@EnableWebSecurity
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

    @SuppressWarnings("deprecation")
    @Bean
    PasswordEncoder passwordEncoder() {
        return NoOpPasswordEncoder.getInstance();
    }

    @Bean
    @Override
    protected UserDetailsService userDetailsService() {
        InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
        manager.createUser(User.withUsername("wanghuan").password("123456").authorities("USER").build());
        manager.createUser(User.withUsername("zhouxingli").password("123456").authorities("USER").build());
        return manager;
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }
}
```

### 创建 OAuth 认证配制类 AuthorizationConfiguration
```
@Component
@Slf4j
@EnableAuthorizationServer
public class AuthorizationConfiguration extends AuthorizationServerConfigurerAdapter {

    @Autowired
    private AuthenticationManager authenticationManager;

    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients.inMemory()
                .withClient("client-id-01")
                .secret("client-secret-01")
                .authorizedGrantTypes("authorization_code" , "password" , "refresh_token")
                .scopes("all")
                .redirectUris("http://localhost");
    }

    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
        endpoints.authenticationManager(authenticationManager);
    }

    @Override
    public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
        security.checkTokenAccess("permitAll()");
    }
}

```

 

### 四种登录方式
