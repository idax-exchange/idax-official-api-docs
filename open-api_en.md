**Chapter 1 : API Overview**

IDAX brings you a powerful and robust suite of APIs. You can select only the features that optimize your trading experience.

IDAX provides APIs for fast access to both spot and contract markets, with the following features:

Market data
Depths of bids and asks
Trade history
K-Line data
Trade execution
Order information
Order history
Account information
In case of any problem, please contact our support team.

**Chapter 2 : Create API**

Get your 'key' and 'secretKey'
'key' is the user's secret key provided by IDAX, 'secretKey' is the private key used to sign your request data. To get your apiKey and SecretKey, go to [Personal Center] -> [My API].

Important Note: Do not reveal your 'key' and 'secretKey' to anyone. They are as important as your password.

**Chapter 3 : Call Mode**

IDAX provides 2 different methods for our users to call our API interface. Developers can adjust based on their own preference to inquire the market, conduct trading.

REST API
REST , a.k.a Respresntational State Transfer, is one of the most common web services protocol. REST emphasis on its readability, standardization, interoperabilty and scalability.Advantages:

Each URL respresented one web resources in RESTful architecture;
Act as a representation of resources between client and server;
Client-end is enabled to operate server-side resources with 4 HTTP requests - representational state transfer.
*We strongly recommend developers to use REST API to proceed spot trading.

WebSocket API
WebSocket protocol is a new HTML5 protocol, which provides full-duplex communication between web browsers and web servers. Connection can be established after one handshake. Web server can then push business logic data to web browsers.Advantages:

Request header is small in size (around 2 bytes) during communication
Web servers and clients can send data bi-directionaly
Since there is no need to create and delete TCP connection repeatedly, it saves resources
*We strongly recommend developers to use Websocket API to access market related information and trading depth.

**Chapter 4 : Sign Params**

Prepare for signing
All parameters except for "sign" must be signed. The parameters must be re-ordered according to the initials of the parameter name, alphabetically.

For example, [placeOrder], The result string is:
amount=1.05&key=123456789&orderSide=buy&orderType=limit&pair=ETH_BTC&price=0.069249&timestamp=1532522823039

MD5 signature
'secretKey' is required to generate HmacSha256 signature. encode the array of bytes using hexadecimal code. Pass the encrypted string to 'sign' parameter. 


**Chapter 5 : Rest API**

The root url is: https://openapi.idax.mn/api/v2  
All requests go over https protocol, The field 'contentType' in request header should be: 'application/json'. 

Request Process 

There are four simple steps to the request process.
1. Request parameter: construct the request parameters according to each API interface.
2. Submit request: use POST or GET method to send request to server.
3. Server respond: server processes request and returns data to users in JSON format after authentication check.
4. Data processing: the client processes the returned data.

**Chapter 6 : API Reference**

> 1.Get /api/v2/time  Get server timestamp

  （1）request demo
  # Request 

```java
  GET https://openapi.idax.mn/api/v2/time
```

  # Response

```java
  {
      "code":10000,
      "msg":"request success",
      "timestamp":1417449600000
  } 
```

  （2）response description

```
  timestamp: 1417449600000：server timestamp
```

> 2．Get /api/v2/ticker  Get Price Ticker

（1）request demo
# Request 

```java
GET https://openapi.idax.mn/api/v2/ticker?pair=ETH_BTC  
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

（2）response description

```
timestamp: server time for returned data
pair: pair
open: best bid
high: highest price
low:  lowest price
last: latest price
vol: volume (in the last rolling 24 hours)
```

（3）request parameters


```
name		type	   require						describe
pair		String		false          IDAX supports trade pairs. Returns a specific trade against the market when specifying a pair,Returns all trading to market prices without specifying pair;

```
> 3．Get /api/api/v2/depth  Get Market Depth

（1）request demo
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

（2）response description

```
asks: ask depth
bids: bid depth
```

（3）request parameters

```
name			type		   require						 	      description
pair			String			true 						     IDAX supports trade pairs
size			Integer			false		              	     value: must be between 1 - 200
merge			Integer			false                            value: 1, 0.00000001 (merge depth)
```

> 4．Get /api/api/v2/trades Get Trade Recently 60

（1）request demo
# Request

```java
GET https://openapi.idax.mn/api/v2/trades?pair=ETH_BTC
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

（2）response description

```
timestamp: transaction time
price:  transaction price
quantity: quantity in BTC (or other coin)
id:  transaction ID
maker: buy/sell
```

（3）request parameters

```
name		type		require			description
pair		String		 true			IDAX supports trade pairs
```

> 5．Get /api/api/v2/kline  approximately 2000 pieces of data are returned each cycle

（1）request demo
# Request

```java
GET https://openapi.idax.mn/api/v2/kline?pair=ETH_BTC&period=1min
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

（2）response description

```
[
	1417449600000,		timestamp
	"2370.16",			open
	"2380",				high
	"2352",				low
	"2367.37",			close
	"17259.83",			volume
]
```

（3）request parameters

```
name		type		 required				description
pair		String			true			IDAX supports trade pairs
period		String			true			1min,5min,15min,30min,1hour,2hour,4hour,6hour,12hour,1day,1week
size		Integer			false	        specify data size to be acquired
since		Long			false           timestamp(eg:1417536000000). data after the timestamp will be returned
```

> 6．POST /api/api/v2/userinfo Get User Account Info

（1）request demo
# Request

```java
POST https://openapi.idax.mn/api/v2/userinfo
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

（2）response description

```
total:total Equity
free:available fund
freezed:frozen fund
```

（3）request parameters

```
name		type  	   required			description
key			String		true     	apiKey of the user
timestamp	Long		true		Request timestamp (valid for 3 minutes)
sign		String		true		signature of request parameters
```

> 7．POST /api/api/v2/placeOrder  Place Orders

（1）request demo
# Request

```java
POST https://openapi.idax.mn/api/v2/placeOrder
```

# Response

```java
{"code":10000, "msg":"request success", "orderId":123456789}
```

（2）response description

```
orderId: order ID
```

（3）request parameters

```
name		type		required			description
key			String		  true			apiKey of the user
pair		String		  true			IDAX supports trade pairs
orderType	String		  true			order type: (limit/market)
orderSide	String		  true	     	order side:(buy/sell)
price		Double		  true			Order price
amount		Double		  true			Order quantity
timestamp	Long		  true			Request timestamp (valid for 3 minutes)
sign		String		  true			signature of request parameters
```

> 8．POST /api/api/v2/tradesHistory Not for Personal

（1）request demo

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

（2）response description

```
timestamp: transaction time
price: transaction price
quantity: quantity in BTC (or other coin)
id: transaction ID
maker:buy / sell
```

（3）request parameters

```
name			type	 required				description
key				String		true			apiKey of the user
pair			String		true			IDAX supports trade pairs
since			Long		true			get 600 pieces of data starting from the given tid (Required)
timestamp		Long		true			Request timestamp (valid for 3 minutes)
sign			String		true			signature of request parameters
```

> 9．POST /api/api/v2/cancelOrder  Cancel the order (support mass order cancellation)

（1）request demo
# Request

```java
POST https://openapi.idax.mn/api/v2/cancelOrder
```

# Response

```java
{"code":10000,"msg":"request success","accepted":"123456789,123456000"}
```

（2）response description

```
accepted:ID(Accepted request for cancellation of order)
```

（3）request parameters

```
name		type	  required			description
key			String		true			apiKey of the user
orderId		String		true			order ID (multiple orders are separated by a comma ',', Max of 5 orders are allowed per request)
timestamp	Long		true			Request timestamp (valid for 3 minutes)
sign		String		true			signature of request parameters
```

> 10．POST /api/api/v2/orderInfo  Get Order Info

（1）request demo
# Request

```java
POST https://openapi.idax.mn/api/v2/orderInfo
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
            "pair": "ETH_BTC",
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
            "pair": "ETH_BTC",
            "orderSide":"sell"
        }],
        "total": 1
}
```

（2）response description

```
quantity: order quantity
timestamp: order time
avgPrice:average transaction price
dealQuantity:filled quantity
orderId:order ID
price:order price
orderState: 1 = unfilled,2 = partially filled, 9 = fully filled, 19 = cancelled
orderSide: buy/sell
total: total number
```

（3）request parameters

```
name		type	  required			description
key			String		true	    apiKey of the user
pair		String		true		IDAX supports trade pairs
orderId		Long		true	  	if order_id is -1, then return all unfilled orders, otherwise return the order specified
pageIndex   int         true        current page number
pageSize    int         true        number of orders returned per page, maximum 100
timestamp	Long		true		Request timestamp (valid for 3 minutes)
sign		String		true    	signature of request parameters
```

> 11．POST /api/api/v2/orderList  Get Order Information in Batch

（1）request demo
# Request

```java
POST https://openapi.idax.mn/api/v2/orderList
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
            "pair": "ETH_BTC",
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
            "pair": "ETH_BTC",
            "orderSide":"sell"
        }]
}
```

（2）response description

```
quantity: order quantity
timestamp: order time
avgPrice:average transaction price
dealQuantity:filled quantity
orderId:order ID
price:order price
orderState: 1 = unfilled,2 = partially filled, 9 = fully filled, 19 = cancelled
orderSide: buy/sell
```

（3）request parameters

```
name         type      required			desctiption
key			String		true		apiKey of the user
pair		String		true		IDAX supports trade pairs
orderId		String		true		order ID (multiple orders are separated by ',', 50 orders at most are allowed per request)
timestamp	Long		true		Request timestamp (valid for 3 minutes)
sign		String		true		signature of request parameters

```

> 12．POST /api/api/v2/orderHistory  Get historical order information and return information only for the last two days

（1）request demo
# Request

```java
POST https://openapi.idax.mn/api/v2/orderHistory
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

（2）response description

```
currentPage:current page number
orders:detailed order information
quantity:order quantity
avgPrice:average transaction price
timestamp:order time
dealQuantity:filled quantity
orderId:order ID
price:order price
orderState:orderState: 1 = unfilled,2 = partially filled, 9 = fully filled, 19 = cancelled
orderSide: 1:buy / 2:sell
pageLength:number of orders per page
total: The total number of records
```

（3）request parameters

```
name			type	 required			description
key				String		true		apiKey of the user
pair			String		true		IDAX supports trade pairs
orderState		Integer		true		query status: 0 for all orders,query status: 0 for unfilled orders, 1 for filled orders  (only the data of the last two days are returned)
currentPage		Integer		true		current page number
pageLength		Integer		true		number of orders returned per page, maximum 100
timestamp		Long		true		Request timestamp (valid for 3 minutes)
sign			String		true		signature of request parameters
```


** senven Common problems of RestAPI**

1、Access restrictions

Answer: Each IP can send maximum of 10 https requests within 1 second. If the 10 limit is exceeded, the system will automatically block the IP for one hour. After that hour, the IP will be automatically unfrozen.
2、Server returns 10000 error code

Answer:All requests go over https protocol, The field 'contentType' in request header should be: 'application/x-www-form-urlencoded'. 




**Chapter 8、The error code**
```java

10000	Successful request processing
10001	System exception/server busy
10002	This IP is not allowed to access
10003	Only support https request
10004	Request frequency too high to exceed the limit allowed	
10005	Non-available API	
10006	Required field, can not be null	
10007	'Api_key' does not exist	
10008	'SecretKey' does not exist
10009	Signature does not match	
10010	API authorization error
10011	Illegal parameter	
10012	IDAX not supports trade pairs
10013	Insufficient funds	
10014	Insufficient coins balance
10015	The Trading volume is less than the minimum requirement	
10016	The transaction amount is less than the minimum requirement	
10017	Trading volume is greater than the maximum volume	
10018	The transaction amount is greater than the maximum requirement	
10019	The deviation between the order price and the latest transaction price is too large	
10020	the Decimal places of Order number over limit	
10021	the Decimal places of Order price over limit	
10022	Order does not exist	
10023	order type is wrong	
10024	Wrong buying and selling direction	
10025	Order failed	
10026	Cancel the order  failure	
10027	User account frozen
10028	Account does not exist	
10029	No trading market information	
10030	No latest market information
10031	No market depth information
10032	incorrect ID
10033	No chart type
......	.....	
10999	According to relevant laws, your country or region cannot use this function。	
