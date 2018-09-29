## 页面事件 {#ye-mian-shi-jian}

### 1. 自动采集 Activity 页面 {#1-zi-dong-cai-ji-activity-ye-mian}

SDK 初始化后，调用如下代码，开启页面自动采集功能：

```
//开启Activity页面追踪
DatatistSDK.getDatatistAutoTrackApi().trackActivityAppViewScreen();
```

忽略一个 Activity 页面采集

```
@DatatistIgnoreTrackAppViewScreen
public class YourActivity extends AppCompatActivity {
}
```

**注意:**一般来说，WebView 所在的 Activity 页面需要添加忽略，因为 H5 会采集页面事件\(H5 集成了 JS SDK\)。

**页面自动采集标题规则：**1.`Activity.getTitle()`获取 2.`getToolbarTtile`获取 3.`Manifest Activity`中设置`label`属性

### 2. 自动采集 Fragment 页面 {#2-zi-dong-cai-ji-fragment-ye-mian}

把 Fragment 当作页面采集，SDK 初始化后，调用如下代码，开启Fragmen自动采集功能：

```
//开启fragment页面追踪
DatatistSDK.getDatatistAutoTrackApi()..trackFragmentAppViewScreen();
```

在需要采集的`Fragment`添加注解`@DatatistTrackFragmentAppViewScreen`，也可以在`BaseFragment`统一添加，重写`"onResume"、"onHiddenChanged"、"setUserVisibleHint"`方法:

```
@DatatistTrackFragmentAppViewScreen
public class BaseFragment extends Fragment {
​
    @Override
    public void onResume() {
        super.onResume();
    }
​
    @Override
    public void onHiddenChanged(boolean hidden) {
        super.onHiddenChanged(hidden);
    }
​
    @Override
    public void setUserVisibleHint(boolean isVisibleToUser) {
        super.setUserVisibleHint(isVisibleToUser);
    }
}
```

不当做页面的 Fragment 请添加忽略注释@DatatistIgnoreTrackAppViewScreen

```
@DatatistIgnoreTrackAppViewScreen
public class MyFragment extends Fragment {
}
```

### 3. 手动页面采集 {#3-shou-dong-ye-mian-cai-ji}

进入某页面时采集所需数据，用于分析页面来源，统计浏览的页面路径，名称。

示例：

```
Track.track().pageview().setTitle("页面标题").setUrl(“当前activity路径”).trackPageview().submit(DatatistSDK.getTracker());
```

建议设置页面采集的位置在 Activity 的 onResume 方法中调用。

## 自定义埋点事件 {#zi-ding-yi-mai-dian-shi-jian}

在需要的位置添加采集代码，记录用户行为或其它所关注的数据，可以用 map 添加自定义的事件属性。

**提示**

* 事件需要在营销云数据管理中的埋点事件中创建。

* 事件属性需要在营销云数据管理中事件属性中创建

**接口声明**

```
public Event customerTrack(@NonNull String eventName, Map<String, Object> udVariable)
```

**参数说明**

* eventName:营销云-数据管理中定义的事件标识符，由英文大小写字母、数字、下划线组成；

* udVariable: 营销云-数据管理中的事件属性，以JSON对象的形式进行传输；

**示例**

```
Map<String, Object> eventMap = new HashMap<>();
eventMap.put("key","value");
eventMap.put("name","张三");
Track.track().customerEvent().customerTrack("营销云-数据管理中的事件标识符",eventMap).submit(DatatistSDK.getTracker());
```

## 预设基础事件 {#yu-she-ji-chu-shi-jian}

### 1. 搜索采集 {#1-sou-suo-cai-ji}

说明：点击搜索按钮时，采集搜索的关键词及关键词来源，如热门推荐词或历史记录。

**接口声明**

```
public Search trackSearch(
    @NonNull String keyword, 
    boolean recommendationSearchFlag, 
    boolean historySearchFlag
)
```

**参数说明**

* keyword：搜索关键词

* recommendationSearchFlag：使用推荐搜索关键词的标志，值为true/false

* historySearchFlag：使用搜索历史记录来进行搜索，值为true/false

示例：

```
Track.track().search().trackSearch("水果之王", true, true).submit(DatatistSDK.getTracker());
```

### 2. 用户注册 {#2-yong-hu-zhu-ce}

说明：用户注册成功的回调中，采集注册信息。注意：第三方授权一键登录时，会自动注册账户，此时也要加注册埋码。

**接口声明**

```
public Register trackRegister(
    @NonNull String uid,
    @NonNull String type, 
    boolean authenticated
)
```

**参数说明**

* uid: 用户注册的用户ID

* type: 用户类型

* authenticated: 是否已认证的标识，值为true/false

示例：

```
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().register().trackRegister(userId,"VIP",true).submit(tracker);
```

### 3. 用户登录 {#3-yong-hu-deng-lu}

说明：用户登录成功的回调中，采集登录信息。

**接口声明**

```
public Login trackLogin(@NonNull String uid)
```

**参数说明**

* uid: 用户ID

示例：

```
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().login().trackLogin("uid-2017101609320300").submit(tracker);
```

### 4. 产品访问页 {#4-chan-pin-fang-wen-ye}

说明：进入商品详情页后调用，采集商品信息。

**接口声明**

```
public ProductPage trackProductPage(
    @NonNull String sku, 
    @NonNull String productCategory1, 
    @NonNull String productCategory2,
    @NonNull String productCategory3, 
    double productOriginalPrice, 
    double productRealPrice
)
```

**参数说明**

* sku: 页面产品的SKU信息

* productCategory1: 一级目录

* productCategory2: 二级目录，没有值就留空

* productCategory3: 三级目录，没有值就留空

* productOriginalPrice: 商品原价

* productRealPrice: 商品优惠后的价格

示例：

```
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().productPage().trackProductPage("商品sku","果蔬", "柑橘类", "澳大利亚脐橙", 100.00, 67.00).submit(tracker);
```

### 5. 加入购物车 {#5-jia-ru-gou-wu-che}

说明：点击加入购物车按钮时调用，采集加入购物车的商品信息。

**接口声明**

```
public Cart trackAddCart(
    @NonNull String sku, 
    long productQuantity, 
    double productRealPrice
)
```

**参数说明**

* sku: 被加入购物车的SKU商品信息

* productQuantity: 被加入购物车的SKU商品数量

* productRealPrice: 被加入购物车的商品单价

示例：

```
TrackerKernel tracker= DatatistSDK.getTracker();
Track.track().cart().trackAddCart(sku, 10000, 67.00,).submit(tracker);
```

### 6. 生成订单 {#6-sheng-cheng-ding-dan}

说明：提交订单时调用，设置订单中的必要参数，采集订单信息。

**接口声明**

```
public Order trackOrder(
    @NonNull OrderInfo orderInfo,
    @NonNull CouponInfo couponInfo,
    @NonNull ProductInfo productInfo
)
```

**参数说明**

* OrderInfo: 订单信息对象，必传，其中包含项5项具体的参数：

* orderID: string订单号

* orderAMT: double 订单总价

* shipAMT: double 运费总价

* shipAddress: string 收货地址

* shipMethod: string 配送方式

* CouponInfo: 优惠券信息对象，传此订单使用的优惠券优惠券对象，不能传null，其中包含2项具体的参数：

* couponType: string 优惠券类型

* couponAMT: string优惠券金额

* ProductInfo: 产品信息对象对象，必传，其中包含6项具体的参数：

* productSKU: string 产品SKU

* productTitle: string 产品名称

* productRealPrice: double产品实际成交价

* productOriPrice: double产品原价，没有则不设置，sdk默认为-1区别于0元商品

* productQuantity: long 产品数量

* productSourceSku: string 活动商品来源（例如赠品产品。传原商品sku，标识原商品的绑定关系）

示例：

```
OrderInfo orderInfo = new OrderInfo("ORDERID ********", 124502.023,332.00, "上海市徐汇区宜山路333号汇鑫国际603", "顺丰快递");
CouponInfo couponInfo = new CouponInfo();
CouponItem couponItem = new CouponItem("满20减1", 1.00);
couponInfo.add(couponItem);
couponItem = new CouponItem("1元红包", 1.00);
couponInfo.add(couponItem);
ProductInfo productInfo = new ProductInfo();
ProductItem productItem = new ProductItem("产品SKU1","榴莲",67.00,100.00,3,"");
productInfo.add(productItem);
productItem = new ProductItem("sku:43557566343546700","菠萝",35.00,45.00,10,"");
productInfo.add(productItem);
productItem = new ProductItem("sku:43557566343547650", "iPhone X", 8900, 2, "");//不含原价数据创建对象的方法
productInfo.add(productItem);
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().order().trackOrder(orderInfo, couponInfo, productInfo).submit(tracker);
```

### 7. 支付订单 {#7-zhi-fu-ding-dan}

说明：支付回调时调用，采集支付信息。

**接口声明**

```
public Payment trackPayment(
    @NonNull String orderID, 
    String payMethod, 
    boolean payStatus, 
    double payAMT
)
```

**参数说明**

* orderID: 订单号

* payMethod: 支付渠道

* payStatus: 支付状态，值为true/false

* payAMT: 支付总金额

示例：

```
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().payment().trackPayment("orderId-24316435A88Y2","支付宝&微信", true, 124502.023).submit(tracker);
```

### 8. 预充值 {#8-yu-chong-zhi}

说明：充值回调时调用，采集充值信息。

**接口声明**

```
public PreCharge trackPreCharge(
    double chargeAMT, 
    @NonNull String chargeMethod, 
    double couponAMT, 
    boolean payStatus
)
```

**参数说明**

* chargeAMT: 充值金额

* chargeMethod: 充值渠道

* couponAMT: 充值优惠金额

* payStatus: 支付状态，值为true/false

示例：

```
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().preCharge().trackPreCharge(1000.00,"微信充值",10,true).submit(tracker);
```

### 9. 极光推送初始化 {#9-ji-guang-tui-song-chu-shi-hua}

说明：极光推送初始化完成后调用，上传极光推送的 RegistrationID，用于营销活动追踪。

**接口声明**

```
public InitJPushEvent trackInitJPush(
    @NonNull String registrationID
)
```

**参数说明**

* registrationID：记录推送的 RegistrationID

示例：

```
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().initJPushEvent().trackInitJPush(registrationID).submit(tracker);
```

### 10. 极光推送事件追踪 {#10-ji-guang-tui-song-shi-jian-zhui-zong}

说明：点击推送消息栏时调用，用于营销活动追踪。

**接口声明**

```
public JPushEvent trackJPush(@NonNull PushInfo pushInfo, @NonNull Intent pushIntent)
```

**参数说明**

* pushInfo： 记录推送的Tag,RegistrationID,Alias

* pushIntent：本次推送的Intent

示例：

```
PushInfo pushInfo = new PushInfo("", registrationID, "");
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().jpushEvent().trackJPush(pushInfo, intent).submit(tracker);
```

### 11. APP下载渠道 {#11-app-xia-zai-qu-dao}

说明：APP打包发布时，在application中按多渠道打包，采集不同渠道的下载量及用户行为。

**接口声明**

```
public DownloadChannel trackDownloadChannel(
@NonNull String downloadChannelName
)
```

**参数说明**

* downloadChannelName： 下载渠道，对应的应用市场名称

示例：

```
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().downloadChannel().trackDownloadChannel("XX应用市场").submit(tracker);
```

### 12. APP打开来源渠道 {#12-app-da-kai-lai-yuan-qu-dao}

说明：从第三方应用打开APP时调用，采集不同来源渠道的用户行为数据。

**接口声明**

```
public OpenChannel trackOpenChannel(@NonNull String openChannelName)
```

**参数说明**

* openChannelName： 从第三方应用打开APP时调用

示例：

```
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().openChannel().trackOpenChannel("从xx应用打开").submit(tracker);
```

### 13. 退出登录 {#13-tui-chu-deng-lu}

说明：退出登录回调中调用，采集用户退出登录 行为数据，登出后的后续行为归为访客行为。

**接口声明**

```
public Logout trackLogout()
```

**参数说明**

* 无

示例：

```
TrackerKernel tracker = DatatistSDK.getTracker();
Track.track().logout().trackLogout(userId).submit(tracker);
```



