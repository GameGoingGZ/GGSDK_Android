# GGSDK Integration

Version:2.6.1

## 集成

1.将 ggbase.aar  放入libs 目录下。

2.app 级别 gradle 下增加

```xml
plugins {
    id 'com.android.application'
    id 'com.google.gms.google-services'
		id 'com.google.firebase.crashlytics'
}
```

```xml
repositories {
    flatDir {
        dirs 'libs'
    }
    mavenCentral()
    mavenLocal()

    google()
    maven { url "https://android-sdk.is.com" }
    maven { url "https://dl-maven-android.mintegral.com/repository/mbridge_android_sdk_oversea" }
    maven { url "https://artifact.bytedance.com/repository/pangle" }
    maven { url "https://sdk.tapjoy.com" }
    maven {
        url "https://maven.google.com"
    }
}
```

```xml
dependencies {
    implementation fileTree(include: ['*.jar','*.aar'], dir: 'libs')
    
    
    implementation 'com.applovin:applovin-sdk:+'
    // Import the Firebase BoM
    implementation platform('com.google.firebase:firebase-bom:31.0.2')

    // When using the BoM, you don't specify versions in Firebase library dependencies

    // Add the dependency for the Firebase SDK for Google Analytics
    implementation 'com.google.firebase:firebase-analytics'
    implementation 'com.google.firebase:firebase-messaging'
    implementation 'com.google.firebase:firebase-crashlytics'

    implementation 'com.google.code.gson:gson:2.10'
    implementation 'com.apkfuns.logutils:library:1.7.5'

    implementation 'com.appsflyer:af-android-sdk:6.9.0'
    implementation "com.android.installreferrer:installreferrer:2.2"

    implementation 'com.blankj:utilcode:1.30.7'

		implementation 'io.github.jeremyliao:live-event-bus-x:1.8.0'
    implementation 'com.jeremyliao:smart-event-bus-base:0.0.1'
    annotationProcessor 'com.jeremyliao:smart-event-bus-compiler:0.0.2'


    implementation 'androidx.webkit:webkit:1.5.0'

}
```

 

```xml
apply plugin: 'applovin-quality-service'
applovin {
    apiKey 'xxxxxx'
}
```

其中apikey 由运营提供，也称为“ad review key”。

3.project 级别gradle  下增加：

```xml

buildscript {
    repositories {
        google()
        jcenter()
        mavenCentral()
        mavenLocal()

        maven { url "https://android-sdk.is.com" }
        maven { url "https://dl-maven-android.mintegral.com/repository/mbridge_android_sdk_oversea" }
        maven { url "https://artifact.bytedance.com/repository/pangle" }
        maven { url "https://sdk.tapjoy.com" }
        maven {
            url "https://maven.google.com"
        }
        maven { url 'https://artifacts.applovin.com/android' }
        maven { url "https://jitpack.io" }

        maven { url "https://maven.google.com" }
    }
    
    dependencies {
       
        classpath "com.applovin.quality:AppLovinQualityServiceGradlePlugin:+"

        classpath 'com.google.gms:google-services:4.3.14'

				classpath 'com.google.firebase:firebase-crashlytics-gradle:2.9.2'

    }
}

allprojects {
    repositories {
        google()
        jcenter()
        maven { url "https://jitpack.io" }

    }
}

```

4.AndroidManifest.xml 增加

```xml
 <meta-data
            android:name="applovin.sdk.key"
            android:value="[xxxxxx]" />

        <service
            android:name="com.ggbase.adapter.firebase.GGFirebaseMessagingService"
            android:exported="false"
            tools:ignore="Instantiatable">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

其中 applovin sdk key  [xxxxxx] 由运营提供。

开启http 支持：

```xml
<application
        xxxxxx
             
        android:usesCleartextTraffic="true">
        
         
</application>
```

5.从运营获得 **google-services.json** 文件，放入app 根目录下。

6.proguard 规则添加：

```
-keep class com.ggbase.** {*;}
```



## 增加广告源

https://dash.applovin.com/documentation/mediation/android/mediation-adapters 

在这个网址中，勾选运营要求的广告源，将自动生成的dependencies 复制到app 级别的gradle 文件中即可。

#### Ad Manager

注意，如果需要添加  Ad Manager,需要在 `AndroidManifest.xml` 文件中添加：

```
  <meta-data
      android:name="com.google.android.gms.ads.AD_MANAGER_APP"
      android:value="true"/>
```

指定Ad Manager Adapter 版本：

```
implementation 'com.applovin.mediation:google-ad-manager-adapter:21.2.0.0'
```

#### Ad Mob

如果需要添加Ad Mob,需要在 `AndroidManifest.xml` 文件中添加：

```
<manifest>
    <application>
        <!-- Sample Ad Manager app ID: ca-app-pub-3940256099942544~3347511713 -->
        <meta-data
            android:name="com.google.android.gms.ads.APPLICATION_ID"
            android:value="ca-app-pub-xxxxxxxxxxxxxxxx~yyyyyyyyyy"/>
    </application>
</manifest>
```

从运营处可获得；

#### Bigo [new]

```
implementation 'com.bigossp:max-mediation:3.0.2.0'
```

#### NetMarvel [new]

将  NMSDK-vx.x.x.aar和 MaxApater-vxxxxx.aar 放入libs 目录下。

## 接口使用

### 初始化

配置参数。

在 Application 中添加

```java
public class MyApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        GGConfig.getInstance()
                .setAppKey("xx")
                .setAppSecret("xx")
                .setUmkAppId("xx")
                .setAfDevKey("xx")//appsflyer dev key
                .setOneLinkID("xx")//appsflyer one link id
                .setVideoUnitId("xx")//视频ID
                .setInsUnitId("xx")//插屏ID
                .setNativeUnitId("xx")//原生广告ID
          			.setNmKey("xxxx")//NetMarvel app key 运营提供，如果不需要可不设
                .setServerUrl("xx")//业务域名
                .setStatUrl("xx")//统计域名
                .setDebug(false)//是否测试环境
                .setPrintLog(true);//是否打印sdk log

        GGLib.trackApplicationCreate(this);//追踪create
    }

    @Override
    public void onTerminate() {
        super.onTerminate();

        GGLib.trackApplicationTerminate(this);//追踪Terminate
    }
}

```

在最先启动的 activity 中调用初始化接口：

```java

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.xxxxx);


        GGLib.init(this, new ActionCallback<UserInfo>() {
            @Override
            public void OnCompleted(boolean success, UserInfo responseBean) {
                
								//初始化完成
               	//loadingLayout.setVisibility(View.GONE);

            }
        });
        
    }
}
```

其中userinfo 结构如下

```java
    /// <summary>
    /// 是否激励用户
    /// </summary>
    public boolean reward ;
    /// <summary>
    /// 用户ID
    /// </summary>
    public String uk;
    /// <summary>
    /// 活跃天数
    /// </summary>
    public int activeDays;
    /// <summary>
    /// 注册天数
    /// </summary>
    public int registerDays;
    /// <summary>
    /// 服务器时间戳
    /// </summary>
    public long  serverTimeStamp ;

    /// <summary>
    /// 服务器时间
    /// </summary>
    public String now;

    /// <summary>
    /// 国家
    /// </summary>
    public String country;
    /// <summary>
    /// 注册时间
    /// </summary>
    public long createTimeStamp ;


    /// <summary>
    /// 邀请码
    /// </summary>
    public String inviteCode;
```

### 广告

#### 激励视频

```java
    /**
     * 展示激励视频广告
     *
     * @param scene    广告场景，开发者自定义，用于统计不同场景的广告表现
     * @param listener 回调事件
     */
GGLib.showVideo("test_video", new GGRewardVideoListener() {
            @Override
            public void onFialed() {

            }

            @Override
            public void onReward() {
                Log.d("开发者奖励用户");
            }
        });
```

#### 插屏

```java


 /**
     * 展示插屏广告
     *
     * @param scene    广告场景，开发者自定义，用于统计不同场景的广告表现
     * @param listener 回调事件
     */
GGLib.showInterstitial("test_ins", new GGInterstitialListener() {
            @Override
            public void onFialed() {
                Log.d("gamegoing", "onFialed: ");
            }

            @Override
            public void onHide() {
                Log.d("gamegoing", "onHide: ");

            }
        });
```

#### 开屏

```java
//冷启动展示开屏广告
//在启动页中，初始化前调用
//注意：ON_OPEN_AD_READY 仅回调一次，并且不一定有回调，开发者注意避免等待过长时间
//GGEvent 能够监听生命周期，如果跳转其他界面前，启动页会销毁，可不移除监听。但是如果未销毁，需要手动移除，防止回调过久触发导致其他界面弹出开屏广告。GGEvent.onAdEvent().removeObserver( your observer);
GGEvent.onAdEvent().observe(this, new Observer<GGAdEvent>() {
    @Override
    public void onChanged(GGAdEvent ggAdEvent) {
        if(ggAdEvent.type == GGAdEvent.Type.ON_OPEN_AD_READY)
        {
            GGLib.showAppOpenAd(new GGAppOpenListener() {
                @Override
                public void onFialed() {

                }

                @Override
                public void onHide() {


                }
            });
        }
      	else if(ggAdEvent.type == GGAdEvent.Type.ON_SHOW)
        {
					//此时广告展示了
          showAd = true;
        }
    }
});


//热启动展示开屏广告需要开发者监听application 生命周期，返回前台时根据需求调用
//如果通过onResume 判断，注意区分是否由于关闭广告触发的/或者首次启动触发的，避免重复弹出广告
//比如
protected void onResume() {
    super.onResume();
    if(!showAd && GGAd.isInitialized())
    {
        GGLib.showAppOpenAd(new GGAppOpenListener() {
            @Override
            public void onFialed() {

            }

            @Override
            public void onHide() {


            }
        });
    }
    showAd = false;
}



```

#### 原生

```java
//展示
/**
 * 显示原生广告【模版样式】
 *
 * @param context   
 * @param scene     广告场景，开发者自定义，用于统计不同场景的广告表现
 * @param container 广告容器 FrameLayout
 * @return
 */
iNativeAd nativeAdapter = SDKLib.getInstance().showNativeAd(MainActivity.this, "test", nativeAdLayout, new GGNativeAdListener() {
            @Override
            public void onFialed() {

            }

            @Override
            public void onShow() {

            }
        });

//销毁
nativeAdapter.destroy();
nativeAdapter = null;

```

原生广告有 【小】和【中】（Small 和 Medium）两种尺寸，【小】最小尺寸为 300 * 120dp ，【中】最小尺寸为 300 * 250dp。当然，高度越高，广告的媒体信息显示越完整。

#### 常规H5广告

开始前请确保开启了**配置获取**以及**初始化H5模块**

```java
//在Application 初始化配置时，增加 setEnableLocalValue
GGConfig.getInstance()
        .setEnableLocalValue(true)
  
  
  
  
//初始化h5模块，SDK初始化完成后执行
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.xxxxx);


        GGLib.init(this, new ActionCallback<UserInfo>() {
            @Override
            public void OnCompleted(boolean success, UserInfo responseBean) {
                
								//初始化完成
              
               	//****初始化H5模块******
              	AdManager.initAdConfig(activity);

            }
        });
        
    }
}

```

接口使用

```java
/**
 * @param  rewardCoins  用户看完广告可以得到的奖励 ，开发者自定义
 * @param  adScenes ⼴告场景，运营提供，⽤于统计不同场景的⼴告表现
 * @param  titleContent h5页面标题栏展示文本
 */
H5Bean h5Bean = new H5Bean("200", "xxx", "测试标题");
//常规H5广告方法 
AdManager.getInstance().showNormalH5Ad(MainActivity.this, h5Bean, new H5AdCallBack() {
    @Override
    public void onFialed(String msg) {
        Log.d(TAG, "onFialed: ");
    }

    @Override
    public void onHide() {
        Log.d(TAG, "onHide: ");
    }

    @Override
    public void onReward() {
        Log.d(TAG, "onReward: ");
    }
});
```

#### 蜂巢H5广告

```java
/**
 * @param  rewardCoins  用户看完广告可以得到的奖励 ，开发者自定义
 * @param  adScenes ⼴告场景，运营提供，⽤于统计不同场景的⼴告表现
 * @param  titleContent h5页面标题栏展示文本
 */
H5Bean h5Bean = new H5Bean("200", "xxx", "测试标题");
//蜂巢H5广告方法 
AdManager.getInstance().showGameH5Ad(MainActivity.this, h5Bean, new H5AdCallBack() {
    @Override
    public void onFialed(String msg) {
        Log.d(TAG, "onFialed: ");
    }

    @Override
    public void onHide() {
        Log.d(TAG, "onHide: ");
    }

    @Override
    public void onReward() {
        Log.d(TAG, "onReward: ");
    }
});
```

### 网络接口

#### 申请提现

```java

/**
 * @param paymentType   支付方式 Paypal,payerMax-PagBank,payerMax-DANA,payerMax-PIX等等中任一种
 * @param account       收款账号
 * @param name          用户名，只有OVO 提现方式需要，其他为""
 * @param amount        提现数额
 * @return
 * @description 申请提现
 * @author wubaozeng
 * @time 10/31/22 11:45 AM
 */
SDKLib.getInstance().redeemRequest(String paymentType, String account, String name, double amount,  ActionCallback<BaseResponseBean> completeCallback)
```



### 统计

```java
  /**
   * 统计
   *
   * @param eventName 事件名
   */
GGAnalysis.track("eventName");
```

```java
  /**
   * 统计
   *
   * @param eventName 事件名
   * @param args      键值对，最终将以 map 形式上报
   */
GGAnalysis.track("eventName","info_key1","info_value1","info_key2",222);
```

注意，trackImmediately 的事件将即时上报，**除了核心打点外，建议少用**，尤其有保活方案的应用。

## 版本记录

### v2.6.1

> 发布时间 2023/7/11
>
> 1.升级Bigo聚合；
>
> 升级说明：
>
> ```
> implementation 'com.bigossp:max-mediation:3.0.2.0'
> ```

### v2.6.0

> 发布时间 2023/6/26
>
> 1.接入Bigo聚合；
>
> 2.更新nm sdk 3.7.5;
>
> 升级说明：
>
> 1.如果需增加bigo 广告源：
>
> ```
> implementation 'com.bigossp:max-mediation:3.0.0.0'
> ```



### v2.5.1

> 发布时间 2023/6/19
>
> 1.优化原生广告精细化控制；
>
> 2.移除部分权限；
>
> 升级说明：
>
> 1.如果需增加bigo 广告源：
>
> ```
> implementation 'com.bigossp:max-mediation:3.0.0.0'
> ```
>

### v2.5.0

> 发布时间 2023/6/5
>
> 1.支持原生广告精细化控制；
>
> 2.修复空指针问题；
>
> 升级说明：
>
> 1.原生广告接口发生变动，需要增加原生广告配置：
>
> ```
>  GGConfig.getInstance()
> 					xxxxxx
>    			.setNativeUnitId("xxxx")//原生
> 					xxxxxx
> ```
>
> 并且展示接口发生[改变](#原生)。
>

### v2.4.0

> 发布时间 2023/5/23
>
> 1.接入 NetMarvel 聚合；
>
> 2.修复空指针问题；
>
> 升级说明：
>
> 1.将 ggbase.aar 和 NMSDK-vx.x.x.aar 放入libs 目录下；
>
> 2.app 级别 gradle 下增加：
>
>     implementation 'androidx.webkit:webkit:1.5.0'
>     implementation "com.android.installreferrer:installreferrer:2.2"
>
> 3.如果需要开启NetMarvel 聚合，需要增加配置nmkey
>
>         GGConfig.getInstance()
>        					xxxxxx
>           			.setNmKey("xxxx")//NetMarvel app key 运营提供，如果不开启可不设
>        					xxxxxx

### v2.3.3

> 发布时间 2023/5/4
>
> 1.修复token和gaid 丢失问题；
>
> 2.游戏配置支持site id 维度筛选；
>
> 4.策略配置支持透传信息作为筛选条件；
>
> 5.支持max 精细化控制；

### v2.3.1

> 发布时间 2023/3/21
>
> 1.修复一些bug;
>

### v2.3.0

> 发布时间 2023/2/15
>
> 1.增加常规H5广告接口和蜂巢H5⼴告接⼝；
>
> 2.增加offer 获取接口；

### v2.2.0

发布时间2023-01-04

1.增加开屏广告和原生广告接口；

修复已知Bug:

> 1).修复广告偶尔id为空或者激励回调失败的情况；
>
> 2).修复收益采集上报错误问题；
