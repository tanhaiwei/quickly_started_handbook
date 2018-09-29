## 埋点事件 {#mai-dian-shi-jian}

### 1 基本事件 {#1-ji-ben-shi-jian}

**1.1 自动页面采集：**

如果初始化时参数AutoTrack设置了true, sdk会自动采集所有控制器的路径和页面标题。

不想采集的页面，需要添加类名到忽略数组中，把你要忽略页面的类名添加到NSArray数组中

```
[[DatatistTracker sharedInstance] trackForbiddenController:<#(NSArray *)#>;
```

建议:

1.webview的控制器忽略，避免与H5的页面重复计算PV

2.父类控制器忽略

**1.2 手动页面采集：**

如果初始化时参数AutoTrack设置了false，请用手动采集页面的方式

进入某页面时采集所需数据，用于分析页面来源，统计浏览的页面路径，名称，访问时长，所使用的浏览器等信息。

```
- (void)trackPageView:(NSString *)views title:(NSString *)title;
```

API说明： 获取每个screen的 view名称和层级路径，并且可以添加自定义参数，建议在每个view controller中调用。

参数说明：

title：页面标题

views：页面路径。

样例程序：

```
// 在 view controllers里检测screen views
- (void)viewDidAppear:(BOOL)animated {
[[DatatistTracker sharedInstance] trackPageView:@"页面路径" title:@"页面标题"];
}
```

**1.3 搜索采集**

说明：点击搜索按钮时，采集搜索的关键词及关键词来源，如热门推荐词或历史记录 。

**接口声明：**

```
- (void)trackSearch:(NSString *)keyword recommendationSearchFlag:(BOOL)recommendationFlag historySearchFlag:(BOOL)historyFlag;
```

API说明：获取搜索栏的关键词和结果信息 。

**参数说明：**

keyword：搜索的关键词

recommendationSearchFlag: 常用词搜索

historySearchFlag：历史搜索

样例程序：

```
// Measure the most popular keywords used for different search  operations in the app
[[DatatistTracker sharedInstance] trackSearch:@"zpf_test" recommendationSearchFlag:false historySearchFlag:false];
```

**1.4 用户注册**

说明：用户注册成功的回调中，采集注册信息。注意：第三方授权一键登录时，会自动注册账户，此时也要加注册埋码

**接口声明：**

```
- (void)trackRegister:(NSString *)uid type:(NSString *)type authenticated:(BOOL)auth;
```

API说明：采集用户注册事件。

**参数说明：**

uid: 用户注册的userID；

type: 用户类型；

authenticated: 是否已认证的标识；

样例程序：

```
[[DatatistTracker sharedInstance] trackRegister:@"1234567890" type:@"register" authenticated:true];
```

**1.5 登录**

说明：用户登录成功的回调中，采集登录信息 。

**接口声明：**

```
- (void)trackLogin:(NSString *)uid;
```

API说明：采集用户登录事件 。

**参数说明：**

uid: 用户ID

样例程序：

```
[[DatatistTracker sharedInstance] trackLogin:@"your_userid"];
```

**1.6 产品页访问**

说明：进入商品详情页后调用，采集商品信息 。

**接口声明：**

```
- (void)trackProductPage:(NSString *)sku productCategory1:(NSString *)category1 productCategory2:(NSString *)category2 productCategory3:(NSString *)category3 productOriginPrice: (double)originPrice productRealPrice:(double)realPrice;
```

API说明：采集产品也信息。

**参数说明：**

sku: 页面产品的SKU信息；

category1: 一级目录；

category2: 二级目录，没有值就留空；

category3: 三级目录，没有值就留空；

originalPrice: 商品原价

realPrice: 商品优惠价

样例程序：

```
// 产品页访问
[[DatatistTracker sharedInstance] trackProductPage:@"4567934501204569" productCategory1:@"电子产品" productCategory2:@"手机" productCategory3:@"安卓手机" productOriginPrice:3999.0 productRealPrice:3000.0];
```

**1.7 加入购物车**

说明：点击加入购物车按钮时调用，采集加入购物车的商品信息 。

**接口声明：**

```
- (void)trackAddCart:(NSString *)sku productQuantity:(long)quantity productRealPrice:(double)realPrice;
```

API说明：采集商品加入购物车事件。

**参数说明：**

sku: 被加入购物车的SKU商品信息；

quantity: 被加入购物车的SKU商品数量；

realPrice: 被加入购物车的商品单价

样例程序：

```
//购物车事件追踪
[[DatatistTracker sharedInstance] trackAddCart:@"4567934501204569" productQuantity:3 productRealPrice:3000.0];
```

**1.8 生成订单**

说明：提交订单时调用，设置订单中的必要参数，采集订单信息。

**接口声明：**

```
- (void)trackOrder:(DatatistOrderInfo *)order couponInfo:(NSArray *)coupons productInfo:(NSArray *)products;
```

API说明：采集创建订单事件。

**参数说明：**

DatatistOrderInfo: 订单信息，其中包含项5项具体的参数：

* orderID: 订单号

* orderAMT: 订单总价

* shipAMT: 运费总价

* shipAddress: 收货地址

* shipMethod: 配送方式

couponInfo为DatatistCouponInfo数组， DatatistCouponInfo: 优惠券信息，传此订单使用的优惠券优惠券对象，其中包含2项具体的参数：

* couponType: 优惠券类型

* couponAMT: 优惠券金额

productInfo为DatatistProductInfo数组， DatatistProductInfo: 产品信息，其中包含6项具体的参数：

* productSKU: 产品SKU

* productTitle: 产品名称

* productRealPrice: 产品实际成交价

* productOriPrice: 产品原价，没有则不设置，sdk默认为-1区别于0元商品

* productSourceSku：活动商品来源（例如赠品产品。传原商品sku，标识原商品的绑定关系）

样例程序：

```
DatatistOrderInfo *orderInfo = [DatatistOrderInfo new]; orderInfo.orderID = @"2017101716591100"; 
orderInfo.orderAMT = 9020.0; orderInfo.shipMethod = @"顺丰快递"; 
orderInfo.shipAddress = @"上海市徐汇区宜山路333号 汇鑫国际1号楼603"; 
orderInfo.shipAMT = 20.0; 
DatatistCouponInfo *couponInfo = [DatatistCouponInfo new]; 
couponInfo.couponType = @"红包%";
couponInfo.couponAMT = 999.002;
​
DatatistCouponInfo *couponInfo2 = [DatatistCouponInfo new];
couponInfo2.couponType = @"优惠券%";
couponInfo2.couponAMT = 99.002;
​
DatatistProductInfo *productInfo = [DatatistProductInfo new];
productInfo.productSKU = @"4567934501204569";
productInfo.productTitle = @"小米 Mix 2";
productInfo.productOriPrice = 3999.0;//没有productOriPrice，可以直接注释掉
productInfo.productRealPrice = 0.01;
productInfo.productQuantity = 3;
productInfo.productSourceSku = @"xxxxxxxxxxxxxxxxx";
​
DatatistProductInfo *productInfo2 = [DatatistProductInfo new];
productInfo2.productSKU = @"45679345012045123";
productInfo2.productTitle = @"华为mate9 ";
productInfo2.productOriPrice = 3999.0;//没有productOriPrice，可以直接注释掉
productInfo2.productRealPrice = 0.01;
productInfo2.productQuantity = 3;
productInfo2.productSourceSku = @"xxxxxxxxxxxxxxxxx";
​
[[DatatistTracker sharedInstance] trackOrder:orderInfo couponInfo:@[couponInfo, couponInfo2] productInfo:@[productInfo, productInfo2]];
```

**1.9 支付订单**

说明：支付回调时调用，采集支付信息 。

**接口声明：**

```
- (void)trackPayment:(NSString *)orderId payMethod:(NSString *)method payStatus:(BOOL)pay payAMT:(double)amt;
```

API说明：采集订单支付事件 。

**参数说明：**

orderID: 订单号

payMenthod: 支付渠道

payStatus: 支付状态，值为true/false

payAMT: 支付总金额

样例程序：

```
[[DatatistTracker sharedInstance] trackPayment:@"2017101716591100" payMethod:@"支付宝" payStatus:true payAMT:9000.0];
```

**1.10 预充值**

说明：充值回调时调用，采集充值信息 。

**接口声明：**

```
- (void)trackPreCharge:(double)amt chargeMethod:(NSString *)chargeMethod couponAMT:(double)coupon payStatus:(BOOL)pay;
```

API说明：采集预充值事件 。

**参数说明：**

chargeAMT: 充值金额

chargeMethod: 充值渠道

couponAMT: 充值优惠金额

payStatus: 支付状态，值为true/false

样例程序：

```
[[DatatistTracker sharedInstance] trackPreCharge:600.0 chargeMethod:@"微信支付" couponAMT:200.0 payStatus:true}];
```

**1.11 极光推送初始化事件**

说明：极光推送初始化完成后调用，上传alias，registrationID（必传），tag 。

**接口声明：**

```
- (void)trackInitJPush:(NSDictionary *)pushManager;
```

API说明：采集极光推送初始化事件 。

**参数说明：**

pushManager：为json对象，包含极光推送的alias，registrationID（必传），tag。

样例程序：

```
NSDictionary *pushManager = @{
@"alias":@"",
@"registrationID":@"12345678789",
@"tag":@""
};
​
[[DatatistTracker sharedInstance] trackInitJPush:pushManager];
```

**1.12 极光推送追踪事件**

说明：点击推送通知栏时调用，采集推送内容，及追踪用户后续行为 。

**接口声明：**

```
- (void)trackJPush:(NSDictionary *)pushInfo pushIntent:(NSDictionary *)pushIntent;
```

API说明：采集极光推送追踪事件 。

**参数说明：**

pushInfo:为json对象，包含极光推送的alias，registrationID（必传），tag。

pushIntent:为通知栏回调时respons.notification.request.content.userInfo对象

样例程序：

```
......
//查看推送消息的回调时
NSDictionary *userInfo = respons.notification.request.content.userInfo;
......
NSDictionary *pushInfo = @{
@"alias":@"",
@"registrationID":@"12345678789",
@"tag":@""
};
​
[[DatatistTracker sharedInstance] trackJPush:pushInfo pushIntent:userInfo];
```

**1.13 APP打开来源渠道**

说明：从第三方应用打开APP时调用，采集不同来源渠道的用户行为数据

**接口声明：**

```
- (void)trackOpenChannel:(NSString *)openChannelName udVariable:(NSDictionary *)vars;
```

API说明： 采集APP打开来源渠道。

**参数说明：**

openChannelName：从第三方应用打开APP时调用。

样例程序：

```
[[DatatistTracker sharedInstance] trackOpenChannel: @"从xx应用打开"];
```

**1.14 退出登录**

说明：退出登录回调中调用，采集用户 退出登录 行为数据，登出后的后续行为归为访客行为

**接口声明：**

```
- (void)trackLogout;
```

API说明： 退出登录。

**参数说明：**

无

样例程序：

```
[[DatatistTracker sharedInstance] trackLogout];
```

### 2 自定义事件 {#2-zi-ding-yi-shi-jian}

说明：在任意位置添加采集代码，记录用户行为或其他你所关注的数据，可以用map添加自定义变量 。

**接口声明：**

```
- (void)customerTrack:(NSString*)name udVariable:(NSDictionary *)vars
```

**参数说明：**

name: 营销云-元事件管理中定义的事件标识符，由英文大小写字母、数字、下划线组成

udVariable: 营销云-元事件管理中的事件属性，以Dictionary对象的形式进行传输

样例程序：

```
 NSDictionary *vars = @{@"address":@"XX街道",@"age":@"18"};
[[DatatistTracker sharedInstance] customerTrack:@"营销云-数据管理中定义的事件标识符" udVariable:vars];
```



