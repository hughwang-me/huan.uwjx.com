---
title: Netty 使用（二）：接收和发送消息
date: 2020-08-31 18:15:35
categories: Netty
summary: Netty 基本使用
tags:
    - Netty
    - IOT
---

## Netty 使用（二）：接收和发送消息

### 1.0 发送消息

 本项目源码：https://github.com/hughwang-me/netty-demo/tree/master/netty-demo-01

```
 @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        log.warn("LogServerHandler -> 设备连接激活");
        String welcomeMsg = "我连接激活了，我是:" + ctx.channel().id().asShortText();
        ByteBuf byteBuf = ByteBufAllocator.DEFAULT.buffer(welcomeMsg.getBytes().length);
        byteBuf.writeBytes(welcomeMsg.getBytes(CharsetUtil.UTF_8));
        ctx.writeAndFlush(byteBuf);
        log.warn("客户端向服务端发送消息完毕 : {}" , welcomeMsg);
    }
``` 

通过 `ctx.channel().id().asShortText()` 获取当前会话的 `id`

或者 `ctx.channel().id().asLongText()` 获取更精确的 `id`

通过 `ByteBuf byteBuf = ByteBufAllocator.DEFAULT.buffer(welcomeMsg.getBytes().length)` 分配一块消息对象 `ByteBuf`

通过 `byteBuf.writeBytes(welcomeMsg.getBytes(CharsetUtil.UTF_8))` 将要发送的内容写入 `ByteBuf` 对象

通过 `ctx.writeAndFlush(byteBuf)` 将消息发送出去 

被发送的消息 会经过配置的过滤器 `Handler` 逐个处理

这里在设备激活连接通道后，向服务器发送一段消息文本。

### 2.0 接收消息

#### 2.1 配置服务端口

```
@Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        log.warn("LogServerHandler -> 设备读取数据");
        ByteBuf byteBuf = (ByteBuf) msg;
        String receiveMsg = byteBuf.toString(CharsetUtil.UTF_8);
        log.warn("客户端接收到消息 -> {}", receiveMsg);
        byteBuf.release();
    }
```

客户端或者服务端都会在收到消息后 经过配制的 `Handler` 逐个处理

然后在 `channelRead` 方法中可以通过 `Object` 对象获取消息
