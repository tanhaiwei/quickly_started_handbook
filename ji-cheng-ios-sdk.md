最新版DatatistTrackers使用ARC并支持IOS8+和OS X10.8+

datatist iOS SDK 支持Object-C和Swift，集成方式支持cocoapods和手动集成，推荐cocoapods方式。

### 使用cocoapods安装 （推荐） {#shi-yong-cocoapods-an-zhuang-tui-jian}

**Object-C项目中**

Podfile 文件添加：

```
pod 'DatatistTracker'
```

执行

```
pod update 或 pod repo update –verbose
pod install
```

**siwft项目中**

Podfile 文件添加：

```
platform :ios,'8.0'
use_frameworks!
target ‘项目名’ do
pod 'DatatistTracker'
end
```

执行

```
pod update 或 pod repo update –verbose
pod install
```

在project 代码根目录中创建DatatistTracker-Bridging-Header.h文件，添加如下.h：

```
#ifndef DatatistTracker_Bridging_Header_h
#define DatatistTracker_Bridging_Header_h
#import <DatatistTracker/DatatistTracker.h>
#endif /* DatatistTracker_Bridging_Header_h */
```

转到Build Settings，在Objectiv-C Bridging Header 中 设置Bridging Header的路径：

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LMPwjXDZfLsn8Vzx6N6%2F-LNDiy3x05E1aLlmLSEk%2F-LNDj098tIVUDHuFWlU9%2FserchPath.png?alt=media&token=4ce06803-29c7-4b8d-975b-ba60e69256bb)

至此，即可在swift中调用DatatistTracker的对象。

### 手动集成 {#shou-dong-ji-cheng}

点击[下载DatatistTracker.zip](http://datatist.com/sourcefile/DatatistTracker.zip)​

解压下载的DatatistTracker.zip文件，将解压后的include文件夹, lib文件夹下的libDatatistTracker.a文件，copy到需要追踪的APP工程的根目录下。也可以根据需要自行指定放置位置。

在需要追踪APP target的Build Setting下，搜索other，将其other linker flags 改成-ObjC。

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LMPwjXDZfLsn8Vzx6N6%2F-LNDiy3x05E1aLlmLSEk%2F-LNDkFUh21p9_0iik5fa%2FObjC参数.png?alt=media&token=91bcd1a3-b3f3-4efb-a7dd-12f65df2fe76)

在需要追踪APP target的Build Setting下，搜索header search paths，将其参数指定为之前include文件夹对应放置的路径。如果是放置在根目录下，则指定为${SOURCE\_ROOT}/include路径。如果是放置到其他位置，则可以根据实际放置的路径设定参数。​

转到Build Phases设置下,在Link Binary With Libraries中添加libDatatistTracker.a。当前的libDatatistTracker.a 已经通过lipo集成了simulator环境和iphoneos环境，可以在开发和发布环境下工作。​

在每个需要引入数据采集的.m 文件中引入如下.h文件

```
#import "DatatistTracker.h"
```

调用相应的API完成数据的采集工作

## 初始化SDK {#chu-shi-hua-sdk}

Appdelegate.m中的didFinishLaunchingWithOptions方法中调用来实现DatatistTracker的初始化。

**配置必要参数：**

```
static NSString * const DatatistProductionServerURL = @"https://tracker.datatist.com/c.gif";
static NSString * const DatatistProductionSiteID = @"xxxxxxxxxx";//营销云上创建应用的siteid
```

**初始化方法（AutoTrack为页面自动采集开关）**

```
[DatatistTracker initWithSiteID:DatatistProductionSiteID 
baseURL:[NSURL URLWithString:DatatistProductionServerURL]
AutoTrack:(BOOL)true];
//设置项目ID
[DatatistTracker sharedInstance].projectId = @“营销云-项目管理中的项目ID”;
```

**showLog设置，是否打印log，用于调试。**

```
[DatatistTracker sharedInstance].showLog = YES
```

**营销云数据开关，控制营销云的数据传输，可用于灰度测试，限流分流等**

```
[[DatatistTracker sharedInstance] enableTrack:(BOOL)true];
```

**重设siteId:某些情况需要重设siteId时，调用如下接口进行设置**

```
[DatatistTracker sharedInstance].resetSiteId = @"新siteId";
```

**GPS定位功能**

在初始化后设置，功能默认关闭

```
[[DatatistTracker sharedInstance] enableGPSTrack:YES];
```

**跨项目数据分发**

APP跳转到内嵌H5时，如果H5也集成了sdk，并且projectId不同。则消息会给APP和H5各发一份。

在初始化后设置，功能默认关闭

```
[DatatistTracker sharedInstance].resetSiteId = @"新siteId";
```

**GPS定位功能**

在初始化后设置，功能默认关闭

```
[[DatatistTracker sharedInstance] enableGPSTrack:YES];
```

**跨项目数据分发**

APP跳转到内嵌H5时，如果H5也集成了sdk，并且projectId不同。则消息会给APP和H5各发一份。

在初始化后设置，功能默认关闭

```
[[DatatistTracker sharedInstance] enableJSProjectIdTrack:YES];
```

## 基础配置 {#ji-chu-pei-zhi}

**userID：**

使用userID可以帮助Tracker收集同用户在不同设备和浏览器上的信息，以长期定位追踪用户信息以及支持跨设备和浏览器行为的关联。userID是一个非空的字符串，比如用户名，邮箱地址，手机号 等唯一识别此用户。userID在不同设备和浏览器上必须是相同的。可以将userID进行加密后进行传输。

示例：

```
[DatatistTracker sharedInstance].userID = @"your_userID";
```

建议设置userID的位置如下：

* 在初始化DatatistTracker后，如果用户已登录，即可设置用户属性

数据云-元事件管理中的自定义用户属性，任意api都可以设置用户属性。设置用户属性之后，其他api也将会带上这些属性，退出登录api会清除用户属性和userID

```
//userProperty其为 定义的字典类型，参数为营销云-元数据管理-用户属性定义的属性key
[DatatistTracker sharedInstance].userProperty = @{@"name":@"datatist",@"age":@"5"};
```

## 版本更新说明 {#ban-ben-geng-xin-shuo-ming}

版本2.2.3：

1. GPS定位 改为默认关闭

2. 新增 可视化圈选功能

3. sdk性能优化

版本2.2.2：

1. 新增GPS定位

2. 新增resetSiteId api

3. 跨项目数据分发

版本2.2.1：

1. 全埋点功能上线

版本2.2.0：

1. 新增 设置项目ID api

2. 新增 设置用户属性 api

3. 新增 自定义事件 api

版本2.1.6：

1. 新增 营销云数据开关功能

2. 优化UIWebview和WKWebview与js的交互

3. 页面自动采集：增加页面忽略api

版本2.1.5：

1. 新增 页面数据自动采集功能

2. 新增 JS与APP数据打通功能

3. 兼容ios7.0

4. 增加退出登录事件

版本2.1.4：

1. 所有行为事件新增自动捕获当前页面路径功能

2. 新增渠道追踪接口

3. 采集APP版本及SDK版本

4. sdk性能优化



