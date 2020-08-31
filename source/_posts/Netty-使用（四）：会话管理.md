---
title: Netty 使用（四）：会话管理
date: 2020-08-31 21:46:31
categories: Netty
summary: Netty 基本使用
tags:
    - Netty
    - IOT
---

## Netty 使用（四）：会话管理

### 1.0 会话上下文和会话ID

 本项目源码：https://github.com/hughwang-me/netty-demo/tree/master/netty-demo-01

#### 1.1 会话上下文
在 `Netty` 中，是通过 `ChannelHandlerContext` 对象来描述上下文的；

在 `Handler` 的实现方法都会有 `ChannelHandlerContext` 对象，比如：
```
  @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception {
        log.warn("LogServerHandler -> channelInactive");
    }

    @Override
    public void channelRegistered(ChannelHandlerContext ctx) throws Exception {
        log.warn("LogServerHandler -> 设备注册完成");

    }
```
通过对 `ChannelHandlerContext` 对象的管理就可以实现会话的管理，发送消息等；

#### 1.2 会话ID

可以通过的 `ChannelHandlerContext` 的 `channel().id()` 获取到 会话ID；
> ctx.channel().id().asShortText()

> ctx.channel().id().asLongText()

### 2.0 创建会话管理类
新建 `CtxManager` 来持久化会话对象
```
@Slf4j
@Component
public class CtxManager {

    private Map<String , ChannelHandlerContext> clients = new HashMap<>();

    public void add(ChannelHandlerContext context){
        clients.put(context.channel().id().asShortText() , context);
    }

    public ChannelHandlerContext get(String channelId){
        return clients.getOrDefault(channelId, null);
    }

    public void remove(String channelId){
        clients.remove(channelId);
    }

    public void remove(ChannelHandlerContext channelHandlerContext){
        clients.remove(channelHandlerContext.channel().id().asShortText());
    }

    public void listAllChannels(){
        clients.forEach((client, context) -> {
            log.warn("客户端 id : {}" , client);
        });
    }

    public void listActiveChannels(){
        clients.forEach((client, context) -> {
            if(context.channel().isActive()){
                log.warn("激活的客户端 id : {}" , client);
            }
        });
    }
}
```
在客户端注册上线时候保存 `ChannelHandlerContext` 对象
```
    @Autowired
    private CtxManager ctxManager;

    @Override
    public void channelRegistered(ChannelHandlerContext ctx) throws Exception {
        //...
        ctxManager.add(ctx);
    }
```

在客户端退出时候移除 `ChannelHandlerContext` 对象
```
    @Override
    public void channelUnregistered(ChannelHandlerContext ctx) throws Exception {
        log.warn("日志处理 -> channelUnregistered");
        ctxManager.remove(ctx);
    }
```

在后续业务开发中，需要下发消息到设备时候，从 `CtxManager` 中取出 `ChannelHandlerContext` 执行指令下发操作；
