## 1.安装SDK {#1安装sdk}

下载[微信小程序sdk](http://datatist.com/sourcefile/%E5%BE%AE%E4%BF%A1%E5%B0%8F%E7%A8%8B%E5%BA%8Fsdk.zip)创建datatist文件夹，并将datatist.js文件放入datatist文件夹中。

在应用口app.js第一行加入如下代码，其他页面调用apiqia前，也需要先获取datatist对象：

```
var datatist = require('datatist/datatist.min.js')
```

在应用入口app.js的App.onLaunch方法调用如下代码作为初始化：

```
datatist.App.init({
      // appID为应用ID，必须填写
      "appID": "xxxxxxx",
      "url_v2": "https://tracker.datatist.com/c.gif",
      "siteId_v2": "营销云-应用管理中的siteId",
      "projectId": "营销云-项目管理中的项目ID",
      //当前小程序的版本号
      "version": "xx.xx.xx",
      //日志开关
      "log":false
    });
```

**营销云数据开关，控制营销云的数据传输，可用于灰度测试，限流分流等：**

```
datatist.setEnable(true);
```

## 2.基础配置 {#2基础配置}

**userID**

使用userID可以帮助Tracker收集同用户在不同设备和浏览器上的信息，以长期定位追踪用户信息以及支持跨设备和浏览器行为的关联。userID是一个非空的字符串，比如用户名，邮箱地址，手机号 等唯一识别此用户。userID在不同设备和浏览器上必须是相同的。可以将userID进行加密后进行传输。

```
var datatist = require('../../datatist/datatist.js')
​
datatist.setUserId("your_userid");
```

**用户属性**

数据云-元事件管理中的自定义用户属性，任意api都可以调用**setUserProperty**设置用户属性，将参数存入**JSON**对象中传递。之后的api将会带上这些属性，退出登录api会清除用户属性和userID

```
setUserProperty: function (udVariable, callback)
```

**参数说明：**

1. udVariable:营销云-元数据管理中定义的用户属性，以JSON对象的形式进行存储；

```
var udVariable = { 
"营销云-元数据管理中定义的用户属性": "xxx"
};
datatist.setUserProperty(udVariable, this.callback)
```

**页面采集**

进入某页面时采集所需数据，用于分析页面来源，统计浏览的页面路径，名称，访问时长，所使用的浏览器等信息。

**接口声明：**

```
trackPageview: function (title, udVariable, callback)
```

**参数说明：**

1. title: 页面标题;

2. udVariable: 客户可扩展的自定义变量，以JSON对象的形式进行存储;

3. callback: 回调接口，暂不支持，直接传null;

```
//进入某页面时采集所需数据
var udVariable={  
"XXXXX":"XXXX"  
};
datatist.trackPageview("页面名称",udVariable, null);
```

**添加请求服务器域名：**

需要在微信小程序里事先设置一个通讯域名，允许sdk数据传输

1. 登陆微信小程序后台，进入设置菜单

2. 打开开发设置，配置服务器域名

3. 在request合法域名中添加：[https://tracker.datatist.com](https://tracker.datatist.com/)​

## 3.事件配置 {#3事件配置}

### 3.1搜索采集 {#31搜索采集}

说明：点击搜索按钮时，采集搜索的关键词及关键词来源，如热门推荐词或历史记录，可以用JSON添加自定义变量。

**接口声明：**

```
trackSearch: function (keyword, recommendationSearchFlag, historySearchFlag, udVariable, callback)
```

**参数说明：其他参数没有值则传空字符串**

1.keyword：搜索关键词

2.recommendationSearchFlag：使用推荐搜索关键词的标志，值为true/false

3.historySearchFlag：使用搜索历史记录来进行搜索，值为true/false

4.udVariable: 客户可扩展的自定义变量，以JSON对象的形式进行存储；

5.callback: 回调接口，暂不支持，直接传null;

示例：

```
//点击搜索按钮时，采集搜索数据  
var udVariable={  
"活动":"水果大促销",  
"XXXXX":"XXXX"  
};  
datatist.trackSearch("纽约时报畅销书", true, false, udVariable, null);
```

### 3.2用户注册 {#32用户注册}

说明：用户注册成功的回调中，采集注册信息，可以用JSON添加自定义变量。注意：第三方授权一键登录时，会自动注册账户，此时也要加注册埋码

**接口声明：**

```
trackRegister: function (userid, type, authenticated, udVariable, callback)
```

**参数说明：其他参数没有值则传空字符串**

1.userid: 用户注册的用户ID；

2.type: 用户类型；

3.authenticated: 是否已认证的标识，值为true/false

4.udVariable: 客户可扩展的自定义变量，以JSON对象的形式进行存储；建议：传注册来源

5.callback: 回调接口，暂不支持，直接传null;

示例：

```
//用户注册成功时，采集注册信息  
var udVariable={  
"用户公司":"datatist",  
"注册来源":"XX第三方授权"  
};  
datatist.trackRegister(userid, type, authenticated, udVariable, null);
```

### 3.3用户登录 {#33用户登录}

说明：用户登录成功的回调中，采集登录信息，可以用JSON添加自定义变量。

**接口声明：**

```
trackLogin: function (userid, udVariable, callback)
```

**参数说明：其他参数没有值则传空字符串**

1.userid: 用户ID

2.udVariable: 客户可扩展的自定义变量，以JSON对象的形式进行传输；建议：传登录来源

3.callback: 回调接口，暂不支持，直接传null;

示例：

```
//用户登录成功时，采集用户信息
var udVariable={
"用户类型":"企业用户",
"登录来源":"XX第三方授权"
};
datatist.trackLogin(userid, udVariable, null);
```

### 3.4用户登出 {#33用户登录-1}

说明：用户登出成功的回调中，清除userID并通知服务器，可以用JSON添加自定义变量。

**接口声明：**

```
trackLogout: function (userid, udVariable, callback)
```

**参数说明：其他参数没有值则传空字符串**

1.userid: 用户ID

2.udVariable: 客户可扩展的自定义变量，以JSON对象的形式进行传输；建议：传登录来源

3.callback: 回调接口，暂不支持，直接传null;

示例：

```
var udVariable={
"用户类型":"企业用户",
"登录来源":"XX第三方授权"
};
datatist.trackLogout(userid, udVariable, null);
```

### 3.5**产品访问页** {#34产品访问页}

说明：进入商品详情页后调用，采集商品信息，可以用JSON添加自定义变量。

**接口声明：**

```
trackProductPage: function (sku, productCategory1, productCategory2, productCategory3, productOriginalPrice, productRealPrice, udVariable, callback)
```

**参数说明：其他参数没有值则传空字符串**

1.sku:页面产品的SKU信息；

2.category1:一级目录；

3.category2:二级目录，没有值就留空；

4.category3:三级目录，没有值就留空；

5.originalPrice:商品原价；

6.realPrice:商品优惠后的价格；

7.udVariable:客户可扩展的自定义变量，以JSON对象的形式进行传输；

8.callback: 回调接口，暂不支持，直接传null;

示例：

```
//进入商品详情页后，采集商品信息
var udVariable={
"参与活动":"XX促销"
};
datatist.trackProductPage('skuxxxx','果蔬','柑橘类','澳大利亚脐橙',100.0,98.0,udVariable,null);
```

### 3.6加入购物车 {#35加入购物车}

说明：点击加入购物车按钮时调用，采集加入购物车的商品信息，可以用JSON添加自定义变量。

**接口声明：仅udVariable可缺省，其他参数没有则传空字符串**

```
trackAddCart: function (sku, productQuantity, productRealPrice, udVariable, callback)
```

**参数说明：**

1.sku:被加入购物车的SKU商品信息；

2.quantity:被加入购物车的SKU商品数量；

3.realPrice:被加入购物车的商品单价

4.udVariable:客户可扩展的自定义变量，以JSON对象的形式进行传输；

5.callback: 回调接口，暂不支持，直接传null;

示例：

```
//点击加入购物车按钮时调用
var udVariable={
"商品类型":"买一赠一活动商品"
};
datatist.trackAddCart('skuxxxx',2,10.0,udVariable, null);
```

### 3.7生成订单 {#36生成订单}

说明：提交订单时调用，设置订单中的必要参数，采集订单信息，可以用JSON添加自定义变量。

**接口声明：**

```
trackOrder: function (orderInfo, couponInfo, productInfo, udVariable, callback)
```

**参数说明：仅udVariable可缺省，其他参数没有则传空字符串，orderInfo,productInfo不能传空**

1.orderInfo: 订单信息，是一个JSON数组，其中包含项5项具体的参数：

* orderID: string订单号

* orderAMT: double 订单总价

* shipAMT: double 运费总价

* shipAddress: string 收货地址

* shipMethod: string 配送方式

2.couponInfo: 优惠券信息，是一个JSON数组，传此订单使用的优惠券优惠券对象，没有可以传空对象，其中包含2项具体的参数：

* couponType: string 优惠券类型

* couponAMT: double 优惠券金额

3.productInfo: 产品信息，是一个JSON数组，其中包含6项具体的参数：

* productSKU: string 产品SKU

* productTitle: string 产品名称

* productRealPrice: double 产品实际成交价

* productOriPrice: double 产品原价，没有则传-1区别于0元商品

* productQuantity: double 产品数量

* productSourceSku: string 活动商品来源（例如赠品产品。传原商品sku，标识原商品的绑定关系）

4.udVariable: 客户可扩展的自定义变量，以JSON对象的形式进行传输。

5.callback: 回调接口，暂不支持，直接传null;

示例：

```
//设置订单中的必要参数  
var orderInfo = {  
"orderID":"xxxxxx",  
"orderAMT":20.0,  
"shipAMT":0.0, 
"shipAddress":"上海市xxxx",  
"shipMethod":"xxx快递"  
};  
//设置优惠券
var couponInfo = 
[
    {
        "couponType": "满20减1",
        "couponAMT": 1.0
    },
    {
        "couponType": "1元红包",
        "couponAMT": 1.0
    }
]; 
//设置产品，例如买一赠一的商品
var productInfo = 
    [
    {
        "productSKU": "菠萝的SKU1",
        "productTitle": “菠萝（买一赠一苹果）",
        "productRealPrice": 12.0,
        "productOriPrice": 11.2,
        "productQuantity": 1,
        "productSourceSku": ""
    },
    {
        "productSKU": "苹果的SKU2",
        "productTitle": " 苹果（买一赠一苹果）",
        "productRealPrice": 2.0,
        "productOriPrice": 1.2,
        "productQuantity": 1,
        "productSourceSku": "菠萝的SKU1"
    }
    ]；  
//提交订单  
var udVariable={  
"参与活动":"免配送费"  
};  
datatist.trackOrder(orderInfo, couponInfo, productInfo, udVariable, null);
```

### 3.8支付订单 {#37支付订单}

说明：支付成功时调用，采集支付信息，可以用JSON添加自定义变量。

**接口声明：**

```
trackPayment: function (orderID, payMethod, payStatus, payAMT, udVariable, callback)
```

**参数说明：仅udVariable可缺省，其他参数没有则传空字符串**

1.orderID:订单号

2.payMethod:支付渠道

3.payStatus:支付状态，值为true/false

4.payAMT:支付总金额

5.udVariable:客户可扩展的自定义变量，以JSON对象的形式进行传输；

6.callback: 回调接口，暂不支持，直接传null;

示例：

```
//支付成功时，记录支付信息
var udVariable={  
"自定义名称":"自定义参数"
};  
datatist.trackPayment('订单ID','xx网银',true,18.2,udVariable, null);
```

### 3.9预充值 {#38预充值}

说明：充值成功时调用，采集充值信息，可以用JSON添加自定义变量。

**接口声明：**

```
trackPreCharge: function (chargeAMT, chargeMethod, couponAMT, payStatus, udVariable, callback)
```

**参数说明：仅udVariable可缺省，其他参数没有则传空字符串**

1.chargeAMT:充值金额

2.chargeMethod:充值渠道

3.couponAMT:充值优惠金额

4.payStatus:支付状态，值为true/false

5.udVariable:客户可扩展的自定义变量，以JSON对象的形式进行传输；

6.callback: 回调接口，暂不支持，直接传null;

示例：

```
//充值成功时，记录充值信息
var udVariable={  
"参与活动":"充100得120"
};  
datatist.trackPreCharge(100.0,'xx网银',20.0,true,udVariable, null);
```

### 3.10退出登录 {#38预充值-1}

说明：退出登录回调中调用，采集用户 退出登录 行为数据，登出后的后续行为归为访客行为

**接口声明：**

```
trackLogout: function (userid, udVariable, callback)
```

**参数说明：仅udVariable可缺省，其他参数没有则传空字符串**

1.userid:用户ID

2.udVariable:客户可扩展的自定义变量，以JSON对象的形式进行传输；

3.callback: 回调接口，暂不支持，直接传null;

示例：

```
var udVariable = {
"自定义参数": "自定义变量"
};
datatist.trackLogout(userid, udVariable, null);
```

## 4.自定义事件 {#4自定义事件}

### 4.1自定义事件 {#41自定义事件}

说明：在任意位置添加采集代码，记录用户行为或其他你所关注的数据，可以用JSON添加自定义变量。

**接口声明：**

```
customerTrack: function (eventName, udVariable, callback)
```

**参数说明：仅udVariable可缺省，其他参数没有则传空字符串**

1.eventName:事件名称；

2.udVariable: 客户可扩展的自定义变量，以JSON对象的形式进行传输；

3.callback: 回调接口，暂不支持，直接传null;

示例：

```
//可以在任意位置记录用户行为或其他数据  
var udVariable={  
"数据云-元事件管理中定义的事件属性":"xxx" 
};  
datatist.customerTrack('数据云-元事件管理中定义的事件标识符',udVariable, null);
```

## 5.版本更新说明 {#4自定义事件-1}

版本2.2.2：

1. 压缩加密sdk

版本2.2.1：

1. 修复部分bug

2. 性能优化

版本2.2.0：

1. 新增projectId设置

2. 新增设置用户属性 setUserProperty api

3. 更新自定义事件API

版本2.1.6：

1. 新增 营销云数据开关功能

2. 新增 退出登录api



