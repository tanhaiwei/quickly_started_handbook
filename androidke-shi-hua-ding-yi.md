## SDK 配置 {#sdk-pei-zhi}

**配置可视化定义的服务器地址**

在 application 的`DatatistSDK.init(this,this)`后，调用如下代码：

```
DatatistSDK.setViewDefineUrl("https://analyzer.datatist.com");
```

如果私有化部署的Js插件地址域名不一致，则需要额外设置：

```
//DatatistSDK.setJsDefinePlugin("https://XXX");
```

**WebView 重写以下方法**

```
//系统 webview 和 X5 webview 都需要重写, 导各自对应的包。
webView.setWebChromeClient(new WebChromeClient() {
    @Override
    public void onProgressChanged(WebView view, int newProgress) {
        super.onProgressChanged(view, newProgress);
    }
});
```

## 进入可视化定义模式流程 {#jin-ru-ke-shi-hua-ding-yi-mo-shi-liu-cheng}

1. 营销云数据管理选择应用打开二维码

2. 扫码使用手机浏览器打开

3. 开启应用

## **注意事项** {#zhu-yi-shi-xiang}

1. Android 需开启应用的悬浮窗权限

## 定义页面 {#ding-yi-ye-mian}

## 定义事件 {#ding-yi-shi-jian}



