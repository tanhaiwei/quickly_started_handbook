# APP与JS数据打通 {#web-js-sdk-接入}

要做到数据打通，APP端和JS端都要集成sdk，并且APP需做相应配置，才能生效

在 App 内有加载一些 H5 页面时，需要将用户行为连贯起来，要做如下配置，打通两端行为数据：

## Android {#1安装sdk}

**系统自带webview**

```
//在webview初始化之后设置
DatatistSDK.getDatatistWebViewInstance(this).showUpWebView(webView,false);
```

**腾讯webview**

```
//在webview初始化之后设置
DatatistSDK.getDatatistWebViewInstance(this).showUpX5WebView(webView,false);
```

## IOS {#1安装sdk-1}

**ios8以上版本，使用bridge交互，做如下配置：**

```
#import "DatatistTracker.h"
#import "WebViewJavascriptBridge.h"
​
- (void)viewDidLoad
{
    WebViewJavascriptBridge *_bridge = [WebViewJavascriptBridge bridgeForWebView:webView];
    [_bridge setWebViewDelegate:self];
    [DatatistTracker sharedInstance].bridge = _bridge;
}
```

**如需兼容ios7，做如下配置：**

1、如果是UIWebView: 在webView加载完成的方法中加入如下代码

```
- (void)webViewDidFinishLoad:(UIWebView *)webView
{
    //从webview上获取相应的JSContext。
    self.context = [webView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
    self.context[@"callHandler"] = ^(NSString *string, NSDictionary *eventBody, id back){
        NSLog(@"sky JavaScriptCore callHandler %@", eventBody);
        [DatatistTracker.sharedInstance trackJSEvent: eventBody];
    };
}
```

2、如果是WKWebView

```
- (void)viewWillAppear:(BOOL)animated {
    [_webView.configuration.userContentController addScriptMessageHandler:self name:@"callHandler"];
}
- (void)viewWillDisappear:(BOOL)animated {
    [_webView.configuration.userContentController removeScriptMessageHandlerForName:@"callHandler"];
}
- (void)userContentController:(WKUserContentController *)userContentController
     didReceiveScriptMessage:(WKScriptMessage *)message {
    if ([message.name isEqualToString:@"callHandler"]) {
        NSDictionary * messageDict = (NSDictionary *)message.body;
        [DatatistTracker.sharedInstance trackJSEvent: messageDict];
    }
}
```

3、如果是用 WebViewJavascriptBridge 的方式交互的

```
- (void)viewDidLoad
{
    WebViewJavascriptBridge *_bridge = [WebViewJavascriptBridge bridgeForWebView:webView];
    [_bridge setWebViewDelegate:self];
    [DatatistTracker sharedInstance].bridge = _bridge;
}
```

## JS {#1安装sdk-2}

不用做其它设置，正常接入SDK调用API。

