---
title: Netty 使用（一）：基本使用
date: 2020-08-31 11:11:17
categories: Netty
summary: Netty 基本使用
tags:
    - Netty
    - IOT
---

## Netty 使用（一）：基本使用

### 1.0 新建 spring-boot-starter 工程，引入最新版本 netty 依赖

 本项目源码：https://github.com/hughwang-me/netty-demo/tree/master/netty-demo-01

```
        <dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty-all</artifactId>
            <version>4.1.51.Final</version>
        </dependency>
``` 

官方文档地址：https://netty.io/wiki/user-guide-for-4.x.html

### 2.0 开发服务器端

#### 2.1 配置服务端口
在 application.yml 中增加如下配置
```
netty:
  port: 9527
```
通过 @Value获取配置信息，检查配置是否正常
```
    @Value("${netty.port}")
    private int port;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        log.warn("Netty服务启动在端口:{}", port);

    }
```

#### 2.2 创建一个 Handler
在 Netty 中， 处理设备通讯是通过一个个Handler拦截器来完成的，一个设备连接到服务器，设备发送数据到服务器，设备被写入数据，数据退出等，都会逐个触发
配置的一组拦截器，我们在每一个方法中加入日志输入，以便基础的监测方法调用；
```
@Component
@Slf4j
@ChannelHandler.Sharable
public class LogHandler extends ChannelInboundHandlerAdapter {

    @Override
    public void channelRegistered(ChannelHandlerContext ctx) throws Exception {
        log.warn("日志处理 -> channelRegistered");
    }

    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        log.warn("日志处理 -> channelActive");
    }

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        log.warn("日志处理 -> channelRead");
    }

    @Override
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
        log.warn("日志处理 -> channelReadComplete");
    }

    @Override
    public void channelWritabilityChanged(ChannelHandlerContext ctx) throws Exception {
        log.warn("日志处理 -> channelWritabilityChanged");
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        log.warn("日志处理 -> exceptionCaught : {}" , cause.getMessage());

    }

    @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception {
        log.warn("日志处理 -> channelInactive");
    }

    @Override
    public void channelUnregistered(ChannelHandlerContext ctx) throws Exception {
        log.warn("日志处理 -> channelUnregistered");
    }
}
```
#### 2.3 创建一个 ChannelInitializer
上面创建的一个个 Handler 是如何注册到 Netty 服务，就是通过 ChannelInitializer 来实现的：
```
@Component
@Slf4j
public class MyChannelInitializer extends ChannelInitializer<Channel> {

    @Autowired
    LogHandler logHandler;

    @Override
    protected void initChannel(Channel ch) throws Exception {
        log.warn("MyChannelInitializer -> initChannel");
        ch.pipeline().addLast(logHandler);
    }
}
```
#### 2.4 实现 Netty Server 基本使用
有了 Handler 和 ChannelInitializer , 就可以来实现一个基本 Netty 服务的使用：
```
@Slf4j
@Component
public class NettyServer implements ApplicationRunner {

    @Value("${netty.port}")
    private int port;

    @Autowired
    MyChannelInitializer myChannelInitializer;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        log.warn("Netty服务启动在端口:{}", port);

        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .childHandler(myChannelInitializer)
                    .option(ChannelOption.SO_BACKLOG, 128)
                    .childOption(ChannelOption.SO_KEEPALIVE, true);

            ChannelFuture f = b.bind(port).sync();

            f.channel().closeFuture().sync();
        } finally {
            workerGroup.shutdownGracefully();
            bossGroup.shutdownGracefully();
        }
    }
}
``` 

#### 2.5 启动 Spring 启动 Netty ，查看相关日志
![启动后日志](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-08-31/20200831-3.png)


### 3.0 开发客户端
#### 3.1 配置IP和端口信息
配置端口的方式和服务端一样，采用相同即可，同时 客户端需要配置HOST，来明确服务端位置；
```
netty:
  host: 127.0.0.1
  port: 9000
```
```
    @Value("${netty.port:#{9527}}")
    int port;
    @Value("${netty.host}")
    String host;
```

#### 3.2 实现客户端的 ChannelInboundHandlerAdapter
同服务端的 Handler 一样，客户端也要定义 Handler 来处理连接交互；

```

@Slf4j
@Component
public class LogHandler extends ChannelInboundHandlerAdapter {

    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        log.warn("LogServerHandler -> 设备连接激活");
    }

    @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception {
        log.warn("LogServerHandler -> channelInactive");
    }

    @Override
    public void channelRegistered(ChannelHandlerContext ctx) throws Exception {
        log.warn("LogServerHandler -> 设备注册完成");
    }

    @Override
    public void channelUnregistered(ChannelHandlerContext ctx) throws Exception {
        log.warn("LogServerHandler -> 设备注销完成");
    }

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        log.warn("LogServerHandler -> 设备读取数据");
    }

    @Override
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
        log.warn("LogServerHandler -> 设备读取数据完成");
    }

    @Override
    public void channelWritabilityChanged(ChannelHandlerContext ctx) throws Exception {
        log.warn("LogServerHandler -> channelWritabilityChanged");
    }
}

```
#### 3.3 实现客户端的 ChannelInitializer
同服务端一样，实现客户端 ChannelInitializer
```
@Slf4j
@Component
public class ClientChannelInitializer extends ChannelInitializer<Channel> {
    
    @Autowired
    LogHandler logHandler;

    @Override
    protected void initChannel(Channel ch) throws Exception {
        ch.pipeline().addLast(logHandler);
    }
}
```

#### 3.4 实现客户端基本功能实现
通过上面实现的 Handler 和 ChannelInitializer，实现客户端基本功能
```
@Slf4j
@Component
public class NettyClient implements ApplicationRunner {

    @Value("${netty.port:#{9527}}")
    int port;
    @Value("${netty.host}")
    String host;

    @Resource
    ClientChannelInitializer clientChannelInitializer;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        log.warn("netty 客户端启动 host:{}" , host);
        log.warn("netty 客户端启动 port:{}" , port);

        EventLoopGroup workerGroup = new NioEventLoopGroup();

        try {
            Bootstrap b = new Bootstrap();
            b.group(workerGroup);
            b.channel(NioSocketChannel.class);
            b.option(ChannelOption.SO_KEEPALIVE, true);
            b.handler(clientChannelInitializer);

            ChannelFuture f = b.connect(host, port).sync();

            f.channel().closeFuture().sync();
        } finally {
            workerGroup.shutdownGracefully();
        }
    }
}
```
#### 3.5 启动客户端
##### 3.5.1 客户端连接日志
![客户端连接日志](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-08-31/20200831-4.png)
##### 3.5.2 服务端响应日志
![务端响应日志](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-08-31/20200831-5.png)
自此，完成了基本的 Netty 客户端与服务器搭建！
