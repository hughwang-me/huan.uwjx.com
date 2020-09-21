---
title: 阿里云推送SDK集成（二） API 集成
date: 2020-09-09 10:56:45
categories: 消息推送 阿里云消息推送 
summary: 阿里云推送SDK集成（二） API 集成
tags:
    - 消息推送
    - 阿里云消息推送
    - Android 消息推送
---

## 阿里云推送SDK集成（二） API 集成

### 文档和资源

```
https://help.aliyun.com/document_detail/181548.html?spm=a2c4g.11186623.6.549.a28f228cFL83wn

https://help.aliyun.com/knowledge_detail/48089.html?spm=a2c4g.11186631.2.5.396d24e1MBre1j
```
本章节源码:
```
https://github.com/hughwang-me/aliyun-push-service
```

### 1.引入依赖

```
        <dependency>
            <groupId>com.aliyun</groupId>
            <artifactId>aliyun-java-sdk-push</artifactId>
            <version>3.13.5</version>
        </dependency>
        <dependency>
            <groupId>com.aliyun</groupId>
            <artifactId>aliyun-java-sdk-core</artifactId>
            <version>4.3.8</version>
        </dependency>
```

### 2.实现消息下发 
```

@Component
@Slf4j
public class PushService implements InitializingBean {

    private static String regionId = "cn-hangzhou";
    @Value("${accessKey}")
    private String accessKey;
    @Value("${accessSecret}")
    private String accessSecret;

    @Override
    public void afterPropertiesSet() throws Exception {
        log.warn("执行启动");
        log.warn("accessKey -> {}" , accessKey);
        log.warn("accessSecret -> {}" , accessSecret);

        DefaultProfile profile = DefaultProfile.getProfile(regionId, accessKey, accessSecret);
        IAcsClient client = new DefaultAcsClient(profile);
        PushRequest request = new PushRequest();
//        request.setRegionId("cn-hangzhou");

        Random random = new Random();
        MsgBean msgBean = new MsgBean();
        msgBean.setClientId("clientId");
        msgBean.setIdentityName("身份证号码");
        msgBean.setIdentityName("王欢");
        msgBean.setSubmitId("12");
        log.warn("发送的消息内容: {}" , JSON.toJSONString(msgBean));

        request.setAppKey(30256251L);
        request.setPushType("MESSAGE");
        request.setDeviceType("ALL");
        request.setTarget("DEVICE");
        request.setTargetValue("406646fd4f6343089d8e7ab1e9773fef");


        request.setBody(JSON.toJSONString(msgBean));
        request.setTitle("测试推送消息Title —> " + random.nextLong());

        request.setAndroidNotifyType("BOTH");
        request.setAndroidOpenType("APPLICATION");
        request.setAndroidPopupActivity("com.jiafinance.mlj.DetailPageActivity");

        request.setStoreOffline(true);

        request.setAndroidRemind(true);
        request.setAndroidPopupTitle("离线推送title");
        request.setAndroidPopupBody("离线推送 body");



        request.setAndroidExtParameters(JSON.toJSONString(msgBean)); //只有发送通知时候生效

        request.setAndroidNotificationChannel("1");

        try {
            PushResponse response = client.getAcsResponse(request);
            log.warn(new Gson().toJson(response));
        } catch (ServerException e) {
            e.printStackTrace();
        } catch (ClientException e) {
            System.out.println("ErrCode:" + e.getErrCode());
            System.out.println("ErrMsg:" + e.getErrMsg());
            System.out.println("RequestId:" + e.getRequestId());
        }
    }
}

``` 
