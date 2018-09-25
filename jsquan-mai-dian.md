## 1. 全埋点开关 {#1安装sdk}

JS全埋点默认为关闭状态，如果需要采集全埋点数据，需要在初始化代码中调用如下api，设置开启全埋点功能。

**接口声明：**

```
setAutoTrack(flag:Boolean);
```

**返回参数：**void；

**参数说明：**

1. flag: 设置true/false控制全埋点开关

**示例：**

```
dtTracker('setAutoTrack', true);
```

## 2. 设置忽略元素 {#1安装sdk-1}

全埋点开启后，会自动采集页面上所有可交互元素，如果需要忽略的元素，需要在初始化代码中调用如下api，设置忽略。

**接口声明：**

```
setAutoTrackSkipElements(elements:Array);
```

**返回参数：**void；

**参数说明：**

1. elements: 目前仅支持由 大写 格式的 html 标签名称的字符串组成的 array；

**示例：**

```
dtTracker('setAutoTrackSkipElements', ['LI', 'A','xxx'])
```

## 3. 设置全埋点上报的元素属性 {#1安装sdk-2}

全埋点功能中，可以设置某种元素属性，对包含此属性的元素进行数据采集。

**接口声明：**

```
setAutoTrackSkipElements(attrs:Array);
```

**返回参数：**void；

**参数说明：**

1. attrs: 元素属性的字符串array；

**示例：**

```
dtTracker('setAutoTrackAttributes', ['href','xxx']);
```



