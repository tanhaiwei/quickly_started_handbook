### 1. 添加**URL Scheme** {#1-tian-jia-url-scheme}

将（datatist.xxxxxxxxxxxxxxxx）到项目中，以便唤醒您的程序进行圈选,其中xxxxxxxxxxxxxxxx为营销云中的siteId; 如下图所示：

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LMPwjXDZfLsn8Vzx6N6%2F-LNDiy3x05E1aLlmLSEk%2F-LNDk536tBSJ-95FtBkh%2FSchemeURL.jpg?alt=media&token=8c58bfd3-a5c4-497b-861f-7c640a222dee)

### 2. 在**AppDelegate**中添加激活圈选的代码 {#2-zai-appdelegate-zhong-tian-jia-ji-huo-quan-xuan-de-dai-ma}

```
- (BOOL)application:(UIApplication *)application openURL:(nonnull NSURL *)url options:(nonnull NSDictionary
<
UIApplicationOpenURLOptionsKey,id
>
 *)options
{
    if ([DatatistTracker handleUrl:url])
    {
        return YES;
    }
    return NO;
}
```

若您在**AppDelegate**中实现了以下一个或多个方法，请在已实现的函数中，调用\[**DatatistTracker handleUrl:**\]

```
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(nullable NSString *)sourceApplication annotation:(id)annotation
- (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary
<
NSString*, id
>
 *)options
```

若以上所有方法均未实现，请实现以下方法并调用\[DatatistTracker handleUrl:\]

```
- (BOOL)application:(UIApplication *)application openURL:(nonnull NSURL *)url options:(nonnull NSDictionary
<
UIApplicationOpenURLOptionsKey,id
>
 *)options
```

### 3. 设置可视化定义的上传地址和引入**JS**代码的地址 {#3-she-zhi-ke-shi-hua-ding-yi-de-shang-chuan-di-zhi-he-yin-ru-js-dai-ma-de-di-zhi}

```
[DatatistTracker sharedInstance].visualDefineUrl = @“https://analyzer.datatist.com”;
[DatatistTracker sharedInstance].visualJSUrl = @"https://analyzer.datatist.com";
```

### 4. 注意事项 {#4-zhu-yi-shi-xiang}

**请确保：**

如果是UIWebVIew,确保实现并执行了 webViewDidFinishLoad:

如果是WKWebVIew,确保实现并执行了 webView:didFinishNavigation:

否则会影响内嵌H5页面的可视化定义

