---
title: 阿里云推送SDK集成（一） Android集成
date: 2020-09-09 10:56:45
categories: 消息推送 阿里云消息推送 
summary: 阿里云推送SDK集成（一） Android集成
tags:
    - 消息推送
    - 阿里云消息推送
    - Android 消息推送
---

## 阿里云推送SDK集成（一） Android集成

### 文档和资源

```
https://help.aliyun.com/document_detail/181548.html?spm=a2c4g.11186623.6.549.a28f228cFL83wn
```
首先在阿里云后台创建好应用，获取 `AppKey` 和 `AppSecret` ，并配置好包名等信息；

### 1.创建 `Android APP` 程序，集成推送的 `SDK` ，配置好相关信息

```
Android APP DEMO 源码 : https://github.com/hughwang-me/AliyunPushDemo
```

#### 1.1 在Project根目录下build.gradle文件中配置maven库URL
在根目录的 `build.gradle` 添加阿里云仓库配置信息
```
allprojects {
    repositories {
        google()
        jcenter()
        maven {
            url 'http://maven.aliyun.com/nexus/content/repositories/releases/'
        }
    }
}
```
#### 1.2 在对应的 app下的 build.gradle 文件中添加对应依赖
在根目录的 `build.gradle` 添加阿里云仓库配置信息

```
        ...
        applicationId "com.jiafinance.mlj"
        ...
        ndk {
            //选择要添加的对应cpu类型的.so库。
            abiFilters 'armeabi', 'x86'
        }
        ...

    implementation 'com.aliyun.ams:alicloud-android-push:3.2.1'
    implementation 'com.aliyun.ams:alicloud-android-third-push:3.2.0'

    implementation 'com.aliyun.ams:huawei-push:2.6.3.305'
    implementation 'com.aliyun.ams:huawei-push-base:2.6.3.305'
    implementation 'com.aliyun.ams:meizu-push:3.8.7.1'
    implementation 'com.aliyun.ams:third_vivopush:2.9.0.1'

```

#### 1.3 新建 `application` 
```
private static final String TAG = "Init";
    @Override
    public void onCreate() {
        super.onCreate();
        initCloudChannel(this);


        HuaWeiRegister.register(this);

        // 注册方法会自动判断是否支持小米系统推送，如不支持会跳过注册。
        MiPushRegister.register(this, "2882303761518349216", "5211834960216");
// OPPO通道注册
        OppoRegister.register(this, "147ac2f5348049b8a5a95515dee47a83", "bad37f0468ae4092b5f683c87ba8a4a0"); // appKey/appSecret在OPPO开发者平台获取
// 魅族通道注册
        MeizuRegister.register(this, "323542", "YYb9x5CDHGjOYft3FcjR"); // appId/appkey在魅族开发者平台获取
// VIVO通道注册
        VivoRegister.register(this);
    }
    /**
     * 初始化云推送通道
     * @param applicationContext
     */
    private void initCloudChannel(Context applicationContext) {
        PushServiceFactory.init(applicationContext);
        CloudPushService pushService = PushServiceFactory.getCloudPushService();
        pushService.register(applicationContext, new CommonCallback() {
            @Override
            public void onSuccess(String response) {
                Log.d(TAG, "init cloudchannel success");
            }
            @Override
            public void onFailed(String errorCode, String errorMessage) {
                Log.d(TAG, "init cloudchannel failed -- errorcode:" + errorCode + " -- errorMessage:" + errorMessage);
            }
        });


        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            NotificationManager mNotificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            // 通知渠道的id。
            String id = "1";
            // 用户可以看到的通知渠道的名字。
            CharSequence name = "notification channel";
            // 用户可以看到的通知渠道的描述。
            String description = "notification description";
            int importance = NotificationManager.IMPORTANCE_HIGH;
            NotificationChannel mChannel = new NotificationChannel(id, name, importance);
            // 配置通知渠道的属性。
            mChannel.setDescription(description);
            // 设置通知出现时的闪灯（如果Android设备支持的话）。
            mChannel.enableLights(true);
            mChannel.setLightColor(Color.RED);
            // 设置通知出现时的震动（如果Android设备支持的话）。
            mChannel.enableVibration(true);
            mChannel.setVibrationPattern(new long[]{100, 200, 300, 400, 500, 400, 300, 200, 400});
            // 最后在notificationmanager中创建该通知渠道。
            mNotificationManager.createNotificationChannel(mChannel);
        }


    }
```

#### 1.4 新建 `MyMessageReceiver`
```
public class MyMessageReceiver extends MessageReceiver {
    @Override
    protected void onNotification(Context context, String s, String s1, Map<String, String> map) {
        Log.e("huan", "onNotification" + s);
        NoticeUtil.send(context , s , s1);
    }

    @Override
    protected void onMessage(Context context, CPushMessage cPushMessage) {
        Log.e("huan", "onMessage -> " + cPushMessage.getContent());
    }

    @Override
    protected void onNotificationOpened(Context context, String s, String s1, String s2) {
        Log.e("huan", "onNotificationOpened");
    }

    @Override
    protected void onNotificationClickedWithNoAction(Context context, String s, String s1, String s2) {
        Log.e("huan", "onNotificationClickedWithNoAction");
    }

    @Override
    protected void onNotificationRemoved(Context context, String s) {
        Log.e("huan", "onNotificationRemoved");
    }

    @Override
    protected void onNotificationReceivedInApp(Context context, String s, String s1, Map<String, String> map, int i, String s2, String s3) {
        Log.e("huan", "onNotificationReceivedInApp" + s);
    }
}
```
#### 1.5 配置 `AndroidManifest.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.jiafinance.mlj">

    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
    <uses-permission android:name="android.permission.VIBRATE"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.GET_TASKS" /><!-- 获取近期运行任务，用于应用是否活跃的判断 -->
    <uses-permission android:name="android.permission.REORDER_TASKS" /><!-- 获取近期运行任务，用于应用是否活跃的判断 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <application
        android:allowBackup="true"
        android:name=".HuanApplication"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">

        <meta-data android:name="com.alibaba.app.appkey" android:value=""/> <!-- 请填写你自己的- appKey -->
        <meta-data android:name="com.alibaba.app.appsecret" android:value=""/> <!-- 请填写你自己的appSecret -->


        <meta-data
            android:name="com.huawei.hms.client.appid"
            android:value=""/>

        <meta-data
            android:name="com.vivo.push.api_key"
            android:value="" />
        <meta-data
            android:name="com.vivo.push.app_id"
            android:value="" />

        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <receiver
            android:name=".MyMessageReceiver"
            android:exported="false"> <!-- 为保证receiver安全，建议设置不可导出，如需对其他应用开放可通过android：permission进行限制 -->
            <intent-filter>
                <action android:name="com.alibaba.push2.action.NOTIFICATION_OPENED" />
            </intent-filter>
            <intent-filter>
                <action android:name="com.alibaba.push2.action.NOTIFICATION_REMOVED" />
            </intent-filter>
            <intent-filter>
                <action android:name="com.alibaba.sdk.android.push.RECEIVE" />
            </intent-filter>
        </receiver>

        <activity android:name=".DetailPageActivity"
            android:exported="true"/>
    </application>

</manifest>
```
#### 1.6 新建 `DetailPageActivity`
```
public class DetailPageActivity extends AndroidPopupActivity {

    TextView detail_page_msg;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.detail_page_layout);
        detail_page_msg = (TextView)findViewById(R.id.detail_page_msg);

    }

    @Override
    protected void onSysNoticeOpened(String s, String s1, Map<String, String> map) {
        Log.w("huan" , "AndroidPopupActivity onSysNoticeOpened" );
        Log.w("huan" , "AndroidPopupActivity onSysNoticeOpened s -> " + s);
        Log.w("huan" , "AndroidPopupActivity onSysNoticeOpened s1 -> " + s1); //离线消息的内容JSON， JSON格式的身份证号码，订单ID等信息
    }
}

```
`DetailPageActivity` 是作为离线时候第三方厂商渠道接收推送消息使用的，接收参数和跳转；
