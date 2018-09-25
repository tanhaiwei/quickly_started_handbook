### 1. 设置全埋点开关 {#1-she-zhi-quan-mai-dian-kai-guan}

IOS全埋点默认为关闭状态，如果需要采集全埋点数据，需要在sdk初始化时调用如下api，开启全埋点功能。

**接口声明：**

```
@property (nonatomic, assign) BOOL enableAutoTrack;
```

**参数说明：**

1. enableAutoTrack: 设置true/false控制全埋点开关

**示例：**

```
//默认为NO
[DatatistTrackersharedInstance].enableAutoTrack = YES;
```

## 2. 设置忽略元素 {#2-she-zhi-hu-lve-yuan-su}

全埋点开启后，默认会自动采集页面上所有可交互元素，如果有需要忽略的元素，需要在初始化代码中调用如下api。

**接口声明：**

```
- (void)trackForbiddenControlClass:(NSArray *)array;
```

**返回参数：**void；

**参数说明：**

1. array: 存储需要忽略的控件的数组

**示例：**

```
//例如忽略采集UIButton 和 UITableVIew
[[DatatistTracker sharedInstance] trackForbiddenControlClass:@[[UIButton class],[UITableView class]]];
```



