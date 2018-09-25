## 1. 全埋点初始化 {#1-quan-mai-dian-chu-shi-hua}

SDK 初始化后，调用如下代码，开启全埋点功能：

```
DatatistSDK.init(this,this);
//开启全埋点
DatatistSDK.getDatatistAutoTrackApi().setAutoTrack(true);
```

**注意：**

如果在**布局xml**中添加`android:onclick`的点击事件，需要监听方法上添加`@DatatistTrackViewOnClick`注解，才能采集事件数据。

XML：

```
<
Button
    android:id="@+id/button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="datatist_button"
    android:onClick="datatistOnClick"/
>
```

示例：

```
@DatatistTrackViewOnClick
public void datatistOnClick(View v) {
​
}
```

## 2. 设置忽略元素 {#2-she-zhi-hu-lve-yuan-su}

### 2.1 忽略某个页面采集或点击 {#21-hu-lve-mou-ge-ye-mian-cai-ji-huo-dian-ji}

**注解方式：**

1. 忽略页面采集：

   `@DatatistIgnoreTrackAppViewScreen`

2. 忽略页面内的元素点击：

   `@DatatistIgnoreTrackAppClick`

3. 同时忽略页面采集和页面内的点击：

   `@DatatistIgnoreTrackAppViewScreenAndAppClick`

   ```
   @DatatistIgnoreTrackAppViewScreen
   public class YourActivity extends AppCompatActivity {
   }
   ```

**代码方式：**

1.忽略单个页面

```
DatatistAutoTrackApi.getInstance().ignoreAutoTrackActivity(Class
<
?
>
 activity);
```

2.忽略多个页面

```
DatatistAutoTrackApi.getInstance().ignoreAutoTrackActivities(List
<
Class
<
?
>
>
 activitiesList);
```

### 2.2 忽略某类型的控件点击 {#22-hu-lve-mou-lei-xing-de-kong-jian-dian-ji}

```
//DatatistAutoTrackApi.getInstance().ignoreViewType(Class viewType)；
//忽略 imageView 控件
DatatistAutoTrackApi.getInstance().ignoreViewType(Image.class)；
```

### 2.3 忽略某个点击监听方法 {#23-hu-lve-mou-ge-dian-ji-jian-ting-fang-fa}

在需要忽略的方法上添加**@DatatistIgnoreTrackOnClick**注释

```
@DatatistIgnoreTrackOnClick
@Override
public void onClick(View view) {
}
```



