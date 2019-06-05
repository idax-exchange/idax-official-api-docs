**一、API概述**

IDAX为用户提供一整套简单而又强大的开发工具，旨在帮助用户快速、高效地将IDAX行情和交易功能整合到自己的应用当中。
IDAX接口是提供服务的基础，开发者在IDAX网站创建账号后，可以根据自身需求建立不同权限的API，并利用API进行自动交易。
通过API可以快速实现以下功能：

接口列表:

|接口数据类型|请求方法| 类 型 |是否需要验签|描述|
| ------------------| ----------------- | ----- |----- |-----------------------|
|系统信息|[/api/v2/time](#1time) | GET  |  N   | 获取服务器时间戳 |
| 交易             |[/api/v2/placeOrder](#2-place-orders) | POST  |  Y   | 下单      |
| 交易             |[/api/v2/cancelOrder](#3-cancel-order) | POST  |  Y   | 撤单        |
| 订单信息 |[/api/v2/orderInfo](#4-order-info) | POST  |  Y   | 获取订单信息 |
| 订单信息 |[/api/v2/orderList](#5-order-list) | POST  |  Y   | 接口停止使用|
| 订单信息 |[/api/v2/orderHistory](#6-order-history) | POST  |  Y   |获取24小时内的订单信息|
| 订单信息 |[/api/v2/beforeOrderHistory](#7-before-order-history) | POST  |  Y   |获取24小时外订单信息|
| 交易信息 |[/api/v2/trades](#8-trades) | GET  |  N   | 获取最近成交信息|
| 交易信息 |[/api/v2/tradesHistory](#9-trade-history) | POST  |  Y   | 接口停止使用|
| 交易信息 |[/api/v2/myTrades](#10-mytrades) | POST  |  Y   |获取我的历史交易信息|
| 账户信息|[/api/v2/userinfo](#11-account-info) | POST  |  Y   |获取我的帐户信息|
| 市场行情  |[/api/v2/ticker](#12-ticker-price) | GET  |  N   | 全部symbol的交易行情 |
| 市场行情  |[/api/v2/depth](#13-depth) | GET  |  N   | 市场深度行情（单个symbol） |
| 市场行情  |[/api/v2/kline](#14-kline) | GET |  N   | 获取K线数据|
| 交易品种信息|[/api/v2/pairs](#15-pairs) | GET  |  N   | 交易所支持的所有交易对|
| 交易品种信息|[/api/v2/pairLimits](#16-pairlimits) | GET  |  N   |获取支持的交易对的最大值、最小值、价格和数量|
| 系统信息|[/api/v2/getSign](#17-getsign) | GET  |  N   | 签名算法验证example |
| 系统信息|[/api/v2/pairRate](#18-pairRate) | GET  |  N   | 法币转换 |


获取接口权限后，可以通过阅读本接口文档来帮助开发。如果您在使用过程中遇到任何问题，请联系我们，将在第一时间为您做出解答。

**二、开启API**

开启API权限
用户的API权限在IDAX平台的[个人中心]->[我的API]获取。点击申请API即可获得，其中key是IDAX提供给API用户的访问密钥，secretKey用于对请求参数签名的私钥。
注意： 请勿向任何人泄露这两个参数，这两个参数关乎您账号的安全。

**三、调用模式**

IDAX为用户提供了两种调用接口的方式，开发者可根据自己的使用场景和偏好，选择适合自己的方式来查询行情、进行交易。

**REST API**

REST，即Representational State Transfer的缩写，是目前流行、通用的一种互联网软件架构。
它结构清晰、标准规范、易于理解、扩展方便，正得到越来越多网站的采用。
其优点如下：

* 在RESTful架构中，每一个URL代表一种资源；
* 客户端和服务器之间，传递这种资源的某种表现层；
* 客户端通过四个HTTP指令，对服务器端资源进行操作，实现“表现层状态转化”。
建议开发者使用REST API进行现货交易等操作。

**WebSocket API**

WebSocket是HTML5开始提供的一种在单个 TCP 连接上进行全双工通讯的协议，使得数据可以快速地双向传播。
通过一次简单的握手就可以建立客户端和服务器连接，服务器根据业务规则可以主动推送信息给客户端。
其优点如下：

* 客户端和服务器进行数据传输时，请求头信息比较小，大概2个字节;
* 客户端和服务器皆可以主动地发送数据给对方；
* 不需要多次创建TCP请求和销毁，节约宽带和服务器的资源。
建议开发者使用WebSocket API获取市场行情和买卖深度等信息。

**四、参数签名**

如何对请求参数进行签名
用户提交的参数除sign参数以外，都必须要参与签名，所有参数名采用驼峰命名，参数名首字母小写。
将待签名字符串按照参数名进行排序（比较所有参数名的第一个字母，按abcd顺序排列，若遇到相同首字母，则看第二个字母，以此类推）。

以【下单交易】的字符串为例，排序后的字符串：
amount=1.05&key=123456789&orderSide=buy&orderType=limit&pair=ETH_BTC&price=0.069249&timestamp=1532522823039

最后，是利用HmacSha256算法，对最终待签名字符串进行签名运算，将得到的字节数组采用16进制码，从而得到签名结果的字符串，该字符串赋值于请求参数sign。

**五、RestAPI请求交互**

REST访问的根URL：https://openapi.idax.pro/api/v2 

每个接口都限制了请求频次（不需要验签的接口，同一IP请求频率<=10次/sec；需要验签的接口，同一key请求频率<=10次/sec）
所有请求基于https协议，请求头中contentType须统一设置为:'application/json’。 

请求交互说明(Rest服务的返回值，均包含了code和msg)：

1、封装请求参数：根据接口请求参数规定进行参数封装。

2、提交请求参数：将封装好的请求参数通过POST或GET方式提交至服务器。

3、服务器响应：服务器将对用户请求数据进行参数安全校验，通过校验后将根据业务逻辑将响应数据以JSON格式返回给用户。

4、响应数据处理：用户对服务器响应数据按需进行处理。

**六、REST API参考**

### 1.time  
> 说明
  获取服务器时间戳
> URL
  /api/v2/time
> 请求方式
  Get
>（1）请求示例
  # Request 

```java
  GET https://openapi.idax.pro/api/v2/time
```

  # Response

```java
  {
      "code":10000,
      "msg":"request success",
      "timestamp":1417449600000
  } 
```

  （2）返回值说明

```
  timestamp: 1417449600000：服务器时间戳
```
### 2．POST /api/api/v2/placeOrder 下单交易（除此接口外，其他接口均采用quantity表示"量"，amount表示"额"）

（1）请求示例
# Request

```java
POST https://openapi.idax.pro/api/v2/placeOrder
```

# Response

```java
{"code":10000, "msg":"request success", "orderId":"2000000000008832432"}
```

（2）返回值说明

```
orderId: 订单ID
```

（3）请求参数

```
参数名		参数类型		必填				描述
key			String			是			用户申请的key
pair		String			是			IDAX支持的交易对
orderType	String			是			limit:限价，market:市价
orderSide	String			是			buy:买，sell:卖
price		Double			是			下单价格
amount		Double			是			下单数量
timestamp	Long			是			请求时间戳（3分钟内有效）
sign		String			是			请求参数的签名
```
### 3．POST /api/api/v2/cancelOrder  撤销订单（支持批量撤单）

（1）请求示例
# Request

```java
POST https://openapi.idax.pro/api/v2/cancelOrder
```

# Response

```java
{"code":10000,"msg":"request success","accepted":"123456789,123456000"}
```

（2）返回值说明

```
accepted:订单ID(已受理的撤单请求)
```

（3）请求参数

```
参数名		参数类型	必填				描述
key			String		是			用户申请的key
orderId		String		是			订单ID(多个订单ID中间以","分隔,一次最多允许撤消5个订单)
timestamp	Long		是			请求时间戳（3分钟内有效）
sign		String		是			请求参数的签名
```
### 4．POST /api/api/v2/orderInfo  获取用户的订单信息

（1）请求示例
# Request

```java
POST https://openapi.idax.pro/api/v2/orderInfo
```

# Response

```java
{
    "code":10000,
    "msg":"request success",
    "orders": [{
            "quantity": "0.1",
            "avgPrice": "0",
            "timestamp": 1418008467000,
            "dealQuantity": "0",
            "orderId": 10000591,
            "price": "500",
            "orderState":1,
            "orderSide":"buy"
        },
        {
            "quantity": "0.2",
            "avgPrice": "0",
            "timestamp": 1417417957000,
            "dealQuantity": "0",
            "orderId": 10000724,
            "price": "0.1",
            "orderState":1,
            "orderSide":"sell"
        }],
        "total": 1
}
```

（2）返回值说明

```
quantity:委托数量
timestamp: 委托时间
avgPrice:平均成交价
dealQuantity:成交数量
orderId:订单ID
price:委托价格
orderState:1:未成交,2:部分成交,9:完全成交,19:已撤单
orderSide: buy/sell
total:总条数
```

（3）请求参数

```
参数名		参数类型	必填			描述
key			String		是		用户申请的key
pair		String		是		IDAX支持的交易对
orderId		String		是		订单ID.-1:所有未完成订单；否则查询指定的订单
pageIndex   int         是      当前页
pageSize    int         是      一页显示多少条
timestamp	Long		是		请求时间戳（3分钟内有效）
sign		String		是		请求参数的签名
```

### 5．POST /api/api/v2/orderList  批量获取用户订单

（1）请求示例
# Request

```java
POST https://openapi.idax.pro/api/v2/orderList
```

# Response

```java
{
    "code":10000,
    "msg":"request success",
    "orders": [{
            "quantity": "0.1",
            "avgPrice": "0",
            "timestamp": 1418008467000,
            "dealQuantity": "0",
            "orderId": 10000591,
            "price": "500",
            "orderState":1,
            "orderSide":"buy"
        },
        {
            "quantity": "0.2",
            "avgPrice": "0",
            "timestamp": 1417417957000,
            "dealQuantity": "0",
            "orderId": 10000724,
            "price": "0.1",
            "orderState":1,
            "orderSide":"sell"
        }]
}
```

（2）返回值说明

```
quantity:委托数量
timestamp: 委托时间
avgPrice:平均成交价
dealQuantity:成交数量
orderId:订单ID
price:委托价格
orderState:1:未成交,2:部分成交,9:完全成交,19:已撤单
orderSide: 1:buy/2:sell
```

（3）请求参数

```
参数名		参数类型	必填			描述
key			String		是		用户申请的key
pair		String		是		IDAX支持的交易对
orderId		String		是		订单ID(多个订单ID中间以","分隔,一次最多允许查询50个订单)
timestamp	Long		是		请求时间戳（3分钟内有效）
sign		String		是		请求参数的签名
```


### 6．POST /api/api/v2/orderHistory  获取最近24小时历史订单信息

>（1）请求示例
# Request

```java
POST https://openapi.idax.pro/api/v2/orderHistory
```

# Response

```java
{
    	"code":10000,
    	"msg":"request success",
		"currentPage": 1,
		"orders": [{
            "quantity": "0.2",
            "avgPrice": "0",
            "timestamp": 1417417957000,
            "dealQuantity": "0",
            "orderId": 10000724,
            "price": "0.1",
            "orderState":1,
            "pair": "ETH_BTC",
            "orderSide":"buy"
		}],
		"pageLength": 1,
		"total": 3
}
```

（2）返回值说明

```
currentPage:当前页码
orders:订单信息
quantity:委托数量
avgPrice:平均成交价
timestamp:委托时间
dealQuantity:成交数量
orderId:订单ID
price:委托价格
orderState:1:未成交,2:部分成交,9:完全成交,19:已撤单
orderSide: 1:buy / 2:sell
pageLength:每页数据条数
total: 总记录数
```

（3）请求参数

```
参数名			参数类型	必填			描述
key				String		是		用户申请的key
pair			String		是		IDAX支持的交易对
orderState		Integer		是		查询状态。-1:所有状态, 0：未完成状态, 1：已完成状态。（只返回最近两天的数据）
currentPage		Integer		是		当前页数
pageLength		Integer		是		每页条数，最多100条
timestamp		Long		是		请求时间戳（3分钟内有效）
sign			String		是		请求参数的签名
```

### 7．POST /api/api/v2/beforeOrderHistory    获取24最近小时外历史订单信息

（1）请求示例
# Request

```java
POST https://openapi.idax.pro/api/v2/beforeOrderHistory
```

# Response

```java
{
    	"code":10000,
    	"msg":"Successful request processing",
		"currentPage": 1,
		"orders": [{
            "quantity": "0.2",
            "avgPrice": "0",
            "timestamp": 1417417957000,
            "dealQuantity": "0",
            "orderId": 10000724,
            "price": "0.1",
            "orderState":1,
            "pair": "ETH_BTC",
            "orderSide":"buy"
		}],
		"pageLength": 1,
		"total": 3
}
```

（2）返回值说明

```
currentPage:当前页码
orders:订单信息
quantity:委托数量
avgPrice:平均成交价
timestamp:委托时间
dealQuantity:成交数量
orderId:订单ID
price:委托价格
orderState:1:未成交,2:部分成交,9:完全成交,19:已撤单
orderSide: 1:buy / 2:sell
pageLength:每页数据条数
total: 总记录数
```

（3）请求参数

```
参数名			参数类型	必填			描述
key				String		是		用户申请的key
pair			String		是		IDAX支持的交易对
orderState		Integer		是		查询状态。-1:所有状态, 0：未完成状态, 1：已完成状态。（只返回最近两天的数据）
currentPage		Integer		是		当前页数
pageLength		Integer		是		每页条数，最多100条
timestamp		Long		是		请求时间戳（3分钟内有效）
sign			String		是		请求参数的签名
orderSide	    Integer		是		0 全部 1 买  2 卖
startTime		Long		是		开始时间
endTime			Long		是		结束时间
```


### 8．Get /api/api/v2/trades 获取IDAX交易信息(60条)

（1）请求示例
# Request

```java
GET https://openapi.idax.pro/api/v2/trades?pair=ETH_BTC
```

# Response

```java
{
	"code":10000,
	"msg":"request success",
	"trades":[{
		"timestamp": 1417449600000,
		"price": "787.71",
		"quantity": "0.003",
		"id": "230433",
		"maker": "buy"
	},
	{
		"timestamp": 1417449600000,
		"price": "787.71",
		"quantity": "0.003",
		"id": "230433",
		"maker": "sell"
	}]
}
```

（2）返回值说明

```
timestamp: 成交时间(ms)
price: 交易价格
quantity: 交易数量
id: 交易记录ID
maker: 成交类型buy / sell
```

（3）请求参数

```
参数名		参数类型		必填			描述
pair		String			是			IDAX支持的交易对
```

### 9．POST /api/api/v2/tradesHistory 获取IDAX历史交易信息（非个人)

（1）请求示例

# Request

```java
POST https://openapi.idax.mn/api/v2/tradesHistory
```

# Response

```java
{
	"code":10000,
	"msg":"request success",
	"trades":[{
		"timestamp": 1367130137,
		"price": "787.71",
		"quantity": "0.003",
		"id": 2304331234,
		"maker":"buy"
	},
	{
		"timestamp": 1367130137,
		"price": "787.71",
		"quantity": "0.003",
		"id": 2304330000,
		"maker":"sell"
	}]
}
```

（2）返回值说明

```
timestamp: 成交时间(ms)
price: 交易价格
quantity: 交易数量
id: 交易生成ID
maker: 成交类型buy / sell
```

（3）请求参数

```
参数名			参数类型	必填				描述
key				String		是			用户申请的key
pair			String		是			IDAX支持的交易对
since			Long		否			since:交易ID，从指定交易id开始获取；未指定时，返回最新成交信息（最多600条数据）
timestamp		Long		是			请求时间戳（3分钟内有效）
sign			String		是			请求参数的签名
```

### 10．POST /api/api/v2/myTrades 获取我的历史交易信息

>（1）请求示例

# Request

```java
POST https://openapi.idax.pro/api/v2/myTrades
```

# Response

```java
{
	"code":10000,
	"msg":"request success",
	"trades":[{
		"timestamp": 1367130137,
		"price": "787.71",
		"quantity": "0.003",
		"pair": "ETH_BTC",
		"maker":"buy"
	},
	{
		"timestamp": 1367130137,
		"price": "787.71",
		"quantity": "0.003",
		"pair": "EOS_BTC",
		"maker":"sell"
	}],
	 "total": 2
}
```

（2）返回值说明

```
timestamp: 成交时间(ms)
price: 交易价格
quantity: 交易数量
pair: 交易对
maker: 成交类型buy / sell
```

（3）请求参数

```
参数名			参数类型	必填				描述
key				String		是			用户申请的key
pair			String		否			IDAX支持的交易对
orderSide	    String		否			buy:买，sell:卖
currentPage		Integer		是		    当前页数
pageLength		Integer		是		    每页条数，最多1000条
startDate		Long		否			开始时间戳
endDate		    Long		否			结束时间戳
timestamp		Long		是			请求时间戳（3分钟内有效）
sign			String		是			请求参数的签名
```

### 11．POST /api/api/v2/userinfo 获取用户账户信息（返回总资产不为0的数据）

>（1）请求示例
# Request

```java
POST https://openapi.idax.pro/api/v2/userinfo
```

# Response

```java
{
	"code":10000,
	"msg":"request success",
	"total": {
		"BTC": "0",
		"ETH": "0"
	},
	"free": {
		"BTC": "0",
		"ETH": "0"
	},
	"freezed": {
		"BTC": "0",
		"ETH": "0"
	}
}
```

（2）返回值说明

```
total:总资产
free:可用
freezed:冻结
```

（3）请求参数

```
参数名		参数类型	必填			描述
key			String		是		用户申请的key
timestamp	Long		是		请求时间戳（3分钟内有效）
sign		String		是		请求参数的签名
```

### 12．Get /api/v2/ticker  获取IDAX行情

>（1）请求示例
# Request 

```java
GET https://openapi.idax.pro/api/v2/ticker?pair=ETH_BTC
```

# Response

```java
{
	"code":10000,
	"msg":"request success",
	"timestamp":1417449600000,
	"ticker":[{
		"pair":"ETH_BTC"
		"open":"33.15",
		"high":"34.15",
		"low":"32.05",
		"last":"33.15",
		"vol":"1053.3919"
	}]
} 
```

（2）返回值说明

```
timestamp: 服务器时间
pair: 交易对名称
open: 开盘价
high: 最高价
low: 最低价
last: 最新成交价
vol: 成交量(最近的24小时)
```

（3）请求参数


```
参数名		参数类型	必填							 描述
pair		String		否    IDAX支持的交易对。指定pair时返回特定交易对市场行情；未指定pair时返回全部交易对市场行情；
```
### 13．Get /api/api/v2/depth  获取IDAX市场深度

>（1）请求示例
# Request

```java
GET https://openapi.idax.mn/api/v2/depth?pair=ETH_BTC
```

# Response

```java
{
	"code":10000,
	"msg":"request success",
	"asks": [
		["792", "5"],
		["789.68", "0.018"],
		["788.99", "0.042"],
		["788.43", "0.036"],
		["787.27", "0.02"]
	],
	"bids": [
		["787.1", "0.35"],
		["787", "12.071"],
		["786.5", "0.014"],
		["786.2", "0.38"],
		["786", "3.217"]
	]
}
```

（2）返回值说明

```
asks:卖方深度[价格，数量]
bids:买方深度[价格，数量]
```

（3）请求参数

```
参数名			 参数类型		必填							 描述
pair			String			是 						    IDAX支持的交易对
size			Integer			否（默认100）				 value: 1-200，单向条数
merge			Integer			否（默认0.00000001深度）	   value: 0~8(行情页面不同交易对支持的小数位可能不同)，合并小数位
```


### 14．Get /api/api/v2/kline 获取IDAX的K线数据（每种周期数据条数2000左右）

>（1）请求示例
# Request

```java
GET https://openapi.idax.pro/api/v2/kline?pair=ETH_BTC&period=1min
```

# Response

```java
{
    "code":10000,
    "msg":"request success",
    "kline":[
        1417449600000,
        "2339.11",
        "2383.15",
        "2322",
        "2369.85",
        "83850.06"
    ]
}
```

（2）返回值说明

```
[
	1417449600000,		时间戳
	"2370.16",			开
	"2380",				高
	"2352",				低
	"2367.37",			收
	"17259.83",			交易量
]
```

（3）请求参数

```
参数名		参数类型		必填					描述
pair		String			是				IDAX支持的交易对
period		String			是				1min,5min,15min,30min,1hour,2hour,4hour,6hour,12hour,1day,1week
size		Integer			否(默认全部获取)	指定获取数据的条数
since		Long			否(默认全部获取)	时间戳。返回指定时间戳前的数据
```

### 15.Get /api/v2/pairs  获取交易所支持的所有交易对

>（1）请求示例
  # Request 

```java
  GET https://openapi.idax.pro/api/v2/pairs
```

  # Response

```java
    {
    	"code": 10000,
    	"msg": "Successful request processing",
    	"pairs": ["DROP_BTC", "VIBE_ETH", "PAT_ETH", "ELEC_ETH", "TRX_USDT", "ANON_ETH", "LTC_BTC", "NER_ETH", "FML_ETH",
        	"C2C_BTC", "MTV_USDT", "AE_ETH", "EOS_BTC", "REN_ETH", "RET_USDT", "LRN_BTC", "MRPH_BTC", "VRS_ETH", "LINDA_BTC", 
        	"WT_ETH", "ZAT_BTC", "SDL_ETH", "FTL_ETH", "OCT_BTC", "VEX_BTC", "TPP_BTC", "WIRE_BTC", "SPD_BTC", "GBC_BTC", 
        	"STORJ_BTC", "BRN_ETH", "WIRE_USDT", "HERC_ETH", "WT_BTC", "ABBC_BTC", "EVE_ETH", "TPP_ETH", "CLO_ETH", "VIBE_USDT", 
        	"EOS_ETH", "XRP_USDT", "AEN_ETH", "STSC_USDT", "DROP_ETH", "WAB_BTC", "ZAT_ETH", "OMG_ETH", "UQC_ETH", "WIT_ETH", 
        	"RET_ETH", "OCT_USDT", "PX_USDT", "LRN_ETH", "MTV_BTC", "VRS_BTC", "CLO_BTC", "CTC_BTC", "OST_BTC", "SNT_ETH", 
        	"TIC_BTC", "NRC_BTC", "TRX_ETH", "BAAS_USDT", "HERC_USDT", "RET_BTC", "TRF_ETH", "EDR_BTC", "DLP_BTC", "ZEN_BTC", 
        	"VIN_ETH", "BOC_ETH", "XET_BTC", "ZING_BTC", "ETC_USDT", "COS_ETH", "GUPASS_ETH", "AE_BTC", "ABTC_BTC", "NULS_BTC", 
        	"TRF_BTC", "VIN_BTC", "ARP_ETH", "UCN_USDT", "LRC_ETH", "FEX_USDT", "OMG_BTC", "UQC_BTC", "HYDRO_ETH", "WIRE_ETH", 
        	"TKGN_ETH", "ABTC_ETH", "BCHABC_BTC", "MTV_ETH", "AEN_BTC", "MRPH_ETH", "LIGHT_ETH", "DASC_BTC", "EVC_ETH", "ETH_USDT", 
        	"STORJ_ETH", "TIC_ETH", "PCCM_BTC", "SPD_ETH", "NRC_ETH", "AIC_USDT", "HBZ_ETH", "TBL_ETH", "EVE_BTC", "BCHSV_BTC", 
        	"FML_BTC", "DIY_ETH", "XET_ETH", "XRP_ETH", "NER_BTC", "KRM_USDT", "MO_BTC", "PLAN_BTC", "DEX_BTC", "DASC_ETH", "FEX_ETH", 
        	"MGO_BTC", "ONTOP_ETH", "ZING_ETH", "POE_BTC", "DIY_USDT", "CYFM_ETH", "PPI_ETH", "SFU_BTC", "CAPP_BTC", "FIC_BTC", 
        	"NULS_ETH", "CPLO_BTC", "TAT_BTC", "EAE_USDT", "NULS_USDT", "VIT_BTC", "FTX_ETH", "LRC_BTC", "ZEN_USDT", "HYDRO_BTC", 
        	"STSC_BTC", "TMC_ETH", "PHR_ETH", "NFFC_ETH", "TRX_BTC", "NTY_USDT", "SCRL_BTC", "CAN_BTC", "PCCM_ETH", "VIT_ETH", 
        	"EVC_BTC", "HBZ_BTC", "ZEN_ETH", "ARN_ETH", "PHR_BTC", "AIC_ETH", "SNT_BTC", "DLP_USDT", "SCRL_ETH", "CTC_ETH", 
        	"DEX_ETH", "MEDIBIT_ETH", "FOTA_BTC", "DPN_ETH", "PSM_BTC", "TUSD_BTC", "NPX_BTC", "DLP_ETH", "FUNDZ_BTC", "FTX_BTC", 
        	"PIX_BTC", "PX_BTC", "FOTA_ETH", "UCN_ETH", "ILC_ETH", "GNAIT_USDT", "ADK_BTC", "PIX_ETH", "XHV_BTC", "WICC_ETH", 
        	"APT_BTC", "TMC_USDT", "NPX_ETH", "IOST_BTC", "PX_ETH", "VIBE_BTC", "HERC_BTC", "SW_BTC", "NIX_BTC", "MCT_ETH", 
        	"APL_ETH", "LIGHT_USDT", "WICC_USDT", "EVE_USDT", "TUSD_USDT", "SHIFT_ETH", "NIX_USDT", "CAPP_USDT", "TAT_ETH", 
        	"WAB_ETH", "SW_ETH", "BAAS_BTC", "ADK_ETH", "TMC_BTC", "MEDIBIT_BTC", "OCT_ETH", "DPN_BTC", "HQT_BTC", "REN_BTC", 
        	"PSM_ETH", "PPI_BTC", "STSC_ETH", "FID_BTC", "C2C_USDT", "FUNDZ_ETH", "ANON_BTC", "BTC_USDT", "TUSD_ETH", "LTC_USDT", 
        	"WIT_BTC", "LGC_ETH", "APL_BTC", "DROP_USDT", "SHIFT_BTC", "ILC_BTC", "CAN_ETH", "XHV_ETH", "UCN_BTC", "GBC_ETH", 
        	"PAT_BTC", "CAPP_ETH", "APIS_BTC", "ETH_BTC", "ARN_BTC", "FIC_ETH", "APT_ETH"]
    } 
```

  （2）返回值说明

```
  pairs: "DROP_BTC"：交易对
```



### 16．GET /api/v2/pairLimits  获取所支持交易对的最大金额，最小金额，价格小数位，数量小数位

（1）请求示例
# Request

```java
GET https://openapi.idax.pro/api/v2/pairLimits?pair=ETH_BTC
```

# Response

```java
{
	"code": 10000,
	"msg": "request success",
	"pairRuleVo": [{
		"pairName": "ETH_BTC",
		"maxAmount": "1000000000000.00000000",
		"minAmount": "0.00100000",
		"priceDecimalPlace": 6,
		"qtyDecimalPlace": 3
	}]
}
```

（2）返回值说明

```
pairName:交易对
maxAmount:最大金额
minAmount:最小金额
priceDecimalPlace:价格小数位
qtyDecimalPlace:数量小数位
```

（3）请求参数

```
参数名		参数类型		必填					描述
pair		String			否				IDAX支持的交易对
```




**七、RestAPI 常见问题**

1、访问限制

答：一个ip每秒钟之内，最多发送10个https请求。若超出请求次数，会自动封ip一个小时，一个小时后自动解除。部分接口还有针对同一key的访问频次限制，具体限制见相关接口，超出后降低访问频次即恢复。

2、服务器返回10000错误码

答：请检查接口所需参数是否正确无误提交给服务器，然后检查请求头信息contentType是否被设置成'application/x-www-form-urlencoded'



## 八, 错误编码

| 编码 | 描述 |
| ------|------|
| 10000 |请求处理成功|
| 10001 |系统异常/服务器忙|
| 10002 |参数不能为空
| 10003 |非法参数
| 10004 |不支持对
| 10005 |该交易不受支持
| 10006 |订单状态是错误的
| 10007 |没有帐户信息
| 10008 |密钥不存在
| 10009 |时间戳为空
| 10010 |时间戳已过期
| 10011 |标志是空的
| 10012 |Ip不在白名单上
| 10013 |签名不匹配
| 10014 |下订单失败
| 10015 |取消订单失败
| 10016 |请求界面太频繁了。请稍后再试
| 10017 |交易所不支持交易对
| 10018 |货币价格是空的
| 10019 |一次最多可以撤销5个订单
| 10020 |订单号为空
| 10021 |只能查询24小时以外的数据
| 10022 |界面暂停服务
| 10023 |秘密是空的
| 10024 |没有交易权
| 10025 |没有查询权限
| 10026 |请求已过期
| 10027 |非法的价格输入
| 10028 |非法数量输入
| 10029 |非法的订单输入
| 10030 |硬币对未知
| 10031 |下单价格小数位错误
| 10032 |下单数量小数位错误
| 10033 |交易金额最大值
| 10034 |交易金额最低
| 10035 |创建订单维护
| 10036 |价格输入为空
| 10037 |金额输入为空
| 10038 |交易不允许在没有上架的情况下进行交易
| 10039 |订阅期间不能出售
| 100100 |订单方面不正确
| 100200 |订单类型不正确
| 100300 |配对状态不正确
| 100400 |价格不能为空
| 100500 |价格太低了
| 100550 |价格太高了
| 100600 |金额太低了
| 100650 |金额太高了
| 100700 |找不到用户
| 100800 |找不到帐户
| 100900 |请求无效
| 101000 |可用金额不够
| 101100 |订单无法取消
| 101200 |取消订单失败
| 101300 |对名称不能为空
| 101400 |要创建的对象已存在
| 101500 |该对象不存在
| 101600 |冷冻量不够
| 101700 |积极行动不存在
| 101800 |没有找到硬币
| 101900 |字符串长度太长
| 102000 |用户被冻结
| 102200 |系统正忙，请稍后再试
| 102300 |反向行动已经存在
| 102400 |GUID不能为空
| 102500 |禁止用户下单
| 102600 |订单属性无效或订单类型为市场
| 102650 |订单模式无效
| 102700 |没有市场价格，请使用NORMAL模式
| 102800 | TriggerPrice必须大于0
| 102900 | PriceGap必须大于0
| 103000 | TrailingStopLoss仅支持市场订单
| 103100 | TriggerPrice太低了
| 103100 | TriggerPrice太高了
| 103100 |不能将资金转移给自己
