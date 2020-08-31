---
title: Netty 使用（三）：过滤器 Handler 处理消息
date: 2020-08-31 18:50:02
categories: Netty
summary: Netty 基本使用
tags:
    - Netty
    - IOT
---

## Netty 使用（三）：过滤器 Handler 处理消息

### 1.0 新增 2 个消息过滤器 `Handler`

 本项目源码：https://github.com/hughwang-me/netty-demo/tree/master/netty-demo-01

#### 1.1 新建 `CodeHandler` 消息过滤器
```
@Slf4j
@Component
public class CodeHandler extends ChannelInboundHandlerAdapter {

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        log.warn("CodeHandler 接收到消息");
        ByteBuf byteBuf = (ByteBuf) msg;
        String receiveMsg = byteBuf.toString(CharsetUtil.UTF_8);
//        byteBuf.release();
        log.warn("CodeHandler channelRead -> {}" , receiveMsg);

        String text = "处理后的消息内容";
        ByteBuf processedMsg = ByteBufAllocator.DEFAULT.buffer(text.getBytes().length);
        processedMsg.writeBytes(text.getBytes(CharsetUtil.UTF_8));

        super.channelRead(ctx , processedMsg);
    }

}
``` 

#### 1.2 新建 `RedisHandler` 消息过滤器
```
@Slf4j
@Component
public class RedisHandler extends ChannelInboundHandlerAdapter {

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        log.warn("RedisHandler 接收到消息");
        ByteBuf byteBuf = (ByteBuf) msg;
        String receiveMsg = byteBuf.toString(CharsetUtil.UTF_8);
        byteBuf.release();
        log.warn("RedisHandler channelRead -> {}" , receiveMsg);
    }
}

``` 

#### 1.3 在 `ClientChannelInitializer` 中添加这 2 个过滤器Handler
```
@Slf4j
@Component
public class ClientChannelInitializer extends ChannelInitializer<Channel> {

    @Autowired
    LogHandler logHandler;
    @Autowired
    CodeHandler codeHandler;
    @Autowired
    RedisHandler redisHandler;

    @Override
    protected void initChannel(Channel ch) throws Exception {
        ch.pipeline().addLast(logHandler);
        ch.pipeline().addLast(codeHandler);
        ch.pipeline().addLast(redisHandler);
    }
}

``` 

增加了 2 个消息过滤器后，当客户端或者服务端接收到消息后，会首先到配置最前的过滤器。
增加方法有 `addLast` `addAfter` `addFirst` 等，需要注意配置的顺序，来控制消息的流转过程；


### 2.0 过滤器处理过程的注意点

#### 2.1 消息应该在最后一个过滤器释放

```
@Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        log.warn("RedisHandler 接收到消息");
        ByteBuf byteBuf = (ByteBuf) msg;
        String receiveMsg = byteBuf.toString(CharsetUtil.UTF_8);
        byteBuf.release();
        log.warn("RedisHandler channelRead -> {}" , receiveMsg);
    }
```

`byteBuf.release();` 如果一旦被调用，后续的过滤器就无法继续处理收到的消息，这个方法应该在最后一个过滤器中被调用；
 
#### 2.2 消息交给下一个过滤器继续处理

```
 @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        log.warn("LogServerHandler -> 设备读取数据");
        ByteBuf byteBuf = (ByteBuf) msg;
        String receiveMsg = byteBuf.toString(CharsetUtil.UTF_8);
        log.warn("客户端接收到消息 -> {}", receiveMsg);
//        byteBuf.release(); 
        super.channelRead(ctx , msg);
    }
```

`super.channelRead(ctx , msg);` 用来将消息继续交给下一个过滤器进行处理，如果不调用该方法，消息不会被继续传递；
最后一个过滤器不需要再调用该方法。

#### 2.3 将处理后的消息交给下一个过滤器

```
@Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        log.warn("CodeHandler 接收到消息");
        ByteBuf byteBuf = (ByteBuf) msg;
        String receiveMsg = byteBuf.toString(CharsetUtil.UTF_8);
//        byteBuf.release();
        log.warn("CodeHandler channelRead -> {}" , receiveMsg); 
        String text = "处理后的消息内容";
        ByteBuf processedMsg = ByteBufAllocator.DEFAULT.buffer(text.getBytes().length);
        processedMsg.writeBytes(text.getBytes(CharsetUtil.UTF_8));

        super.channelRead(ctx , processedMsg);
    }
```

`super.channelRead(ctx , processedMsg)` 这里的 `processedMsg` 是被当前过滤器处理后的消息;

需要注意 `super.channelRead(ctx , msg)` 的调用位置，以便正确的将处理完的消息交给下一个过滤器；

比如： 
`LogHandler` 作为第 1 个过滤器，将原始消息保存到数据库，可能是 16 进制的未解析的原始数据；
`CodeHandler` 作为第 2 个过滤器，将原始消息解析转码后交给下一个过滤器，如将 16 进制的消息转换为业务数据，可以是对象，包含温度值、湿度值、经纬度等；
`RedisHandler` 作为第 3 个过滤器，将解析转码后的，业务需要的消息存入 Redis；
后续可以再增加其他的业务处理流程过滤器，进行业务处理；

![消息处理日志](https://uwjx-production-public-read.oss-cn-hangzhou.aliyuncs.com/huan.uwjx.com/2020-08-31/20200831-6.png)
