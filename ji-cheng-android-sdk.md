**Android Studio 环境下**

1.Project 目录下`build.gradle`添加以下配置:

```
buildscript {
    repositories {
        maven { url "https://dl.bintray.com/datatist/maven" }
        //...
    }
    dependencies {
        ...
        //datatist-android-plugin
        classpath 'com.datatist.android:gradle-plugin:1.1.0'
    }
}
allprojects {
    repositories {
        maven { url "https://dl.bintray.com/datatist/maven" }
        //...
    }
}
```

2.在主 module 的`build.gradle`文件中添加`com.datatist.android`插件、SDK 依赖：

```
apply plugin: 'datatist-android-plugin'//插件配置
​
android {
    defaultConfig {
        //...scheme url 格式： datatist.siteId; 营销云上创建应用的siteid
         def datatist_url_scheme = "datatist." + "XXXXXXXXXX".toLowerCase()
         resValue("string", "datatist_url_scheme", datatist_url_scheme)
    }
}
​
...
dependencies {
    ...
    implementation 'com.datatist.android:DatatistSdk:2.2.4'
}
```

3.AndroidManifest.xml 配置

```
<manifest 
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />

<application
    android:name=".MyApplication"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    <activity android:name=".MainActivity">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>

        <!--请添加这里的整个 datatist_url_scheme intent-filter 区块 -->
        <intent-filter>
            <data android:scheme="@string/datatist_url_scheme"/>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
        </intent-filter>
       <!--请添加这里的整个 datatist_url_scheme intent-filter 区块 -->
</activity>
</manifest>
```

4.SDK 中会使用到SwitchCompat、TabLayout、RecyclerView控件，以及`@NonNull`被用来标注给定的参数或者返回值不能为`null`。需要添加以下依赖\( 如果项目中已引入了，可以不添加 \)：

```
implementation 'com.android.support:appcompat-v7:25.4.0'
implementation 'com.android.support:support-annotations:25.4.0'
implementation 'com.android.support:recyclerview-v7:25.4.0'
implementation 'com.android.support:design:25.4.0'
```

**适配版本：**

* Android SDK 要求最低系统版本为 API 9（Android 2.3）。

* 全埋点功能要求系统最低版本为 API 14 （Android 4.0）。

* 如果 API 低于14 将不能采集`Activity`和`Fragment`的`pageView`事件，以及可视化定义功能。

**注意事项：**

运行时出现`Verification error in java.lang.Object org.datatist.sdk.autotrack.aspectj.FragmentAspectj`。 在`gradle.properties`中添加`android.enableD8=false`禁用 D8 的新编译器；

## 初始化SDK {#初始化sdk}

在项目中继承的 Application 的`onCreate()`方法中初始化：

```
public class MyApplication extends Application implements IDatatist {
​
    @Override
    public void onCreate() {
        super.onCreate();
        initDatatist();
    }
​
    private void initDatatist(){
        DatatistSDK.setSupportV1(false); 
        DatatistSDK.init(this, this, true);
        //为了确保数据采集准确性，在application类中创建管理TrackerKernel。
        TrackerKernel trackerKernel = DatatistSDK.getTracker();
        //设置营销云-项目管理-项目ID
        DatatistSDK.setProjectId("XXXXXXXXXXXXX");
        //全埋点初始化
        DatatistAutoTrackApi datatistAutoTrackApi = DatatistSDK.getDatatistAutoTrackApi();
        //开启全埋点
        datatistAutoTrackApi.setAutoTrack(true);
        //开启Activity页面追踪
        datatistAutoTrackApi.trackActivityAppViewScreen();
        //开启fragment页面追踪
        datatistAutoTrackApi.trackFragmentAppViewScreen();
    }
​
    @Override
    public String setTrackerUrl() {
        return "https://tracker.datatist.com/c.gif";//数据上报地址
    }
​
    @Override
    public String setSiteId() {
        return "your_siteID";//营销云上创建应用的siteid
    }
​
    @Override
    public String setOTrackerUrl() {
        return null;//不接入1.0，则不需要此参数
    }
​
    @Override
    public String setOSiteId() {
        return null;//不接入1.0，则不需要此参数
    }
}
​
```

### 多进程APP的初始化 {#多进程app的初始化}

APP 在集成过第三方 SDK 后，如百度地图，极光推送等，会变成多进程 APP。在多进程APP中集成时，避免出现重复初始化 SDK 或重复调用 Api 现象，需要控制在主线程里进行初始化配置代码如下：

```
@Override
public void onCreate() {
    super.onCreate();
    if (DatatistUtils.isMainProcess(this)){
        initDatatist();
    }
}
```

### 基础配置 {#基础配置}

**1. 设置 userId**

使用userID可以帮助Tracker收集同用户在不同设备和浏览器上的信息，以长期定位追踪用户信息以及支持跨设备和浏览器行为的关联。userID是一个非空的字符串，比如用户名，邮箱地址，手机号 等唯一识别此用户。userID在不同设备和浏览器上必须是相同的。可以将userID进行加密后进行传输。

```
Track.track().tracker().setUserId("your userID");
```

**建议设置userID的位置如下：**

在 Application 中初始化 SDK 后，设置用户保存的userId。

**2. 设置用户属性**

营销云-元事件管理中的自定义用户属性，任意 Api都可以调用`setUserProperty`设置用户属性，将参数存入 JSON 对象中传递。之后的 Api 将会带上这些属性，退出登录 Api 会清除用户属性和 userID。

```
JSONObject jsonObject = new JSONObject();
jsonObject.put("age",24);
jsonObject.put("address","中国上海");
//直接设置(可以在应用启动的地方)
Track.track().tracker().setUserProperty(jsonObject);
​
//其他接口调用，例如login
Track.track().login().trackLogin("userId")
.setUserProperty(jsonObject)
.submit(DatatistSDK.getTracker());
```

### 其它配置 {#其它配置}

**1. 数据上报开关**

默认开启。控制营销云的数据传输，可用于灰度测试，限流分流等。

```
Datatist.setEnable(true);
```

**2. 重设 siteId**

用于同一App，向不同siteid发送数据场景。

```
DatatistSDK.getTracker().resetSiteId("营销云上创建应用的siteId");
```

**3. 跨项目数据分发**

默认关闭。APP跳转到内嵌H5时，如果H5也集成了 SDK，并且 projectId 不同。则数据会给 APP 和 H5 各发一份。

```
Datatist.setMultiProjectId(true);
```

**4. 事件携带经纬度信息**

默认关闭。事件上报增加经纬度参数，如需使用，需打开 GPS 定位权限。

```
Datatist.setAutoTrackGPS(true);
```

### 代码混淆 {#代码混淆}

如果您启用了代码混淆，请在您的`proguard-rules.pro`文件中添加以下代码：

```
-keep class org.datatist.sdk.** {
 *;
}
-dontwarn org.datatist.sdk.**
​
-keep class **.R$* {
    <fields>;
}
-keepnames class * implements android.view.View$OnClickListener
-keepnames class * extends android.view.View
-keepnames class android.support.design.widget.**{
*;
}
​
-keep class * extends android.app.Fragment {
 public void setUserVisibleHint(boolean);
 public void onHiddenChanged(boolean);
 public void onResume();
 public void onPause();
}
-keep class android.support.v4.app.Fragment {
 public void setUserVisibleHint(boolean);
 public void onHiddenChanged(boolean);
 public void onResume();
 public void onPause();
}
-keep class * extends android.support.v4.app.Fragment {
 public void setUserVisibleHint(boolean);
 public void onHiddenChanged(boolean);
 public void onResume();
 public void onPause();
}
​
# 如果使用了 DataBinding
-dontwarn android.databinding.**
-keep class android.databinding.** { *; }
-keep class 您项目的包名.databinding.** {
    <fields>;
    <methods>;
}
```

## 版本更新说明 {#版本更新说明}

**版本2.2.3：**

1. GPS定位 改为默认关闭

2. 新增 可视化圈选功能

3. sdk性能优化

**版本2.2.2：**

1. 新增GPS定位

2. 跨项目数据分发

**版本2.2.1.1：**

1. JS数据打通兼容 X5Webview

**版本2.2.1：**

1. 全埋点功能上线

2. sdk集成方式改成依赖

3. 新增fragment页面采集

**版本2.2.0：**

1. 新增 设置项目ID api

2. 新增 设置用户属性 api

3. 新增 自定义事件 api

4. 页面自动采集初始化修改，DatatistAutoApi类不再使用

**版本2.1.6：**

1. 新增 营销云数据开关

**版本2.1.5：**

1. 新增 页面数据自动采集功能

2. 新增 JS与APP数据打通功能

3. 新增 退出登录api

**版本2.1.4:**

1. 新增打开来源渠道接口

2. 新增下载渠道接口

3. 采集APP版本及SDK版本

4. sdk性能优化



