# Overview

IDAX provide powerful and robust APIs to help user write program to automatic the tradings. With this guide you can learn how to use APIs.

The main features of the API is as following:

* get latest market data easily
* get depths of the orderbook easily
* get the latest trades for speicific trading pairs
* get the K-Line data easily
* query account info easily
* quickly place and cancel orders
* get order details easily

If you have any problem when using APIs , pls contact our support team.

## 1, General Information

* The base endpoint is [https://openapi.idax.pro/api/v2](https://openapi.idax.pro/api/v2)
* All endpoints return either a JSON object or array.
* All time and timestamp related fields are in milliseconds.
* Any endpoint can return an ERROR; the error payload is as following:

```json
{
  "code": 10007,
  "msg": "key does not exist"
}
```

* All url is case-sensitive.
* All parameter name is in Camel-Case style and case-sensitive.
* All request in http-post, the parameters should be sererilized in json format and in body payload.
* All request in http-get, the parameters is in query string.

## 2, Get start

Before get start, you need get the api key and secret first. The 'api key' is a unique string IDAX allocated for you (you can think it as the speicial username used in api) and the 'secret' is the private key used to sign your request data(and you can think it as the password for api).

You can get the apiKey and secret key from idax website : https://www.idax.mn.

To get the API key and secret, please visit the "My API" links in user center.(only available on PC website)

> *WARNINGS* : \
> The api key and secret is security related, it is as *IMPORTANT* as your password. so *DO NOT* use it in any place you don't know well to prevent it leak out. 

## 3, The REST API and Websocket API

IDAX provides both REST API and Websocket API :

### REST API

 REST , a.k.a Respresntational State Transfer, is one of the most common web services protocol. REST emphasis on its readability, standardization, interoperabilty and scalability.

REST API has following advantages:

* Each URL respresented one web resources in RESTful architecture;
* Act as a representation of resources between client and server;
* Client-end is enabled to operate server-side resources with 4 HTTP requests - representational state transfer.

> We strongly recommend developers to use REST API to proceed spot trading.

### WebSocket API

WebSocket protocol is a new HTML5 protocol, which provides full-duplex communication between web browsers and web servers. Connection can be established after one handshake. Web server can then push business logic data to web browsers.

Websocket has follwoing advantages:

* Request header is small in size (around 2 bytes) during communication
* Web servers and clients can send data bi-directionaly
* Since there is no need to create and delete TCP connection repeatedly, it saves resources

> We strongly recommend developers to use Websocket API to access market related information and trading depth.

## 4, Sign Params

All parameters (include both http querystring and http reqeust body) need to be signed with the secret key before submit to the server.

sign parameters typically has 3 steps.

* step 1 : re-order parameters according to the parameter name, alphabetically.
* step 2 : make HmacSha256 calculate use the ordered parameters as the input.
* step 3 : add 'sign' to the parameters and submit data to server.

Here is a step-by-step example of how to send a vaild signed payload from the Linux command line using echo, openssl, and curl.

apikey and secret:

| Name | Value |
|-----|-------|
| key | VmhrcrQEF3ATxV2JtVMEH4dFpGEmYzixOL4VrvAeR2COXtc9pzXbvFV1jLbFXEQf |
| secret | UdfW0hs7uIXPBzS6EE7yMhciZdMPu5rnlw3UoqiihZQWSgzT3fbsGsDNb66WFMNz |

parameters:

| Parameter Name | Parameter Value |
| --------- | ----- |
| pair      |ETH_BTC|
| orderType | limit |
| orderSide | buy   |
| price     | 0.034775 |
| amount    | 1.05  |
| timestamp | 1532522823039 |

step 1:
sort parameters alphabetically (include 'key'):

```bash
amount=1.05&key=VmhrcrQEF3ATxV2JtVMEH4dFpGEmYzixOL4VrvAeR2COXtc9pzXbvFV1jLbFXEQf&orderSide=buy&orderType=limit&pair=ETH_BTC&price=0.034775&timestamp=1532522823039
```

step 2:
hmacSha256 calculation:

```bash
echo -n "amount=1.05&key=VmhrcrQEF3ATxV2JtVMEH4dFpGEmYzixOL4VrvAeR2COXtc9pzXbvFV1jLbFXEQf&orderSide=buy&orderType=limit&pair=ETH_BTC&price=0.034775&timestamp=1532522823039"| openssl dgst -sha256 -hmac "UdfW0hs7uIXPBzS6EE7yMhciZdMPu5rnlw3UoqiihZQWSgzT3fbsGsDNb66WFMNz"
(stdin)= c69d8ec9e274dd20126972b2dfaedc8c74cf06fbb19f968eedcc0a300a95b9f6
```

step 3:
add sign to the parameters and send request to server

```bash
curl -H "Content-Type: application/json" -X POST "https://openapi.idax.mn/api/v2/placeOrder" --data '{"amount": 1.05,"key": "VmhrcrQEF3ATxV2JtVMEH4dFpGEmYzixOL4VrvAeR2COXtc9pzXbvFV1jLbFXEQf","orderSide": "buy","orderType": "limit","pair": "ETH_BTC","price": 0.034775,"sign": "c69d8ec9e274dd20126972b2dfaedc8c74cf06fbb19f968eedcc0a300a95b9f6","timestamp":1532522823039}'
```

## 5, Rest API

* The base endpoint is : [https://openapi.idax.mn/api/v2](https://openapi.idax.mn/api/v2)
* All requests go over https protocol
* The field 'contentType' in request header should be: 'application/json'. 

### Request Process

There are four simple steps to the request process.

1. Request parameter: construct the request parameters according to each API interface.
2. Submit request: use POST or GET method to send request to server.
3. Server respond: server processes request and returns data to users in JSON format after authentication check.
4. Data processing: the client processes the returned data.

## 6, API Reference

### 1, Server Time

> Description

Get Server timestamp

> URL

/api/v2/time

> Http Method

GET

> Request

```bash
curl https://openapi.idax.mn/api/v2/time
```

> Response

```json
{
    "code":10000,
    "msg":"request success",
    "timestamp":1536318118202 --server timestamp
}
```

### 2, Ticker Price

> Description

Get the price of specific ticker.

> URL

/api/v2/ticker

> Http Method

GET

> Request

```bash
curl https://openapi.idax.mn/api/v2/ticker?pair=ETH_BTC
```

> Response

```json
{
    "code":10000,
    "msg":"request success",
    "timestamp":1536320917805, //server time for returned data
    "ticker":
    [
        {
            "pair":"ETH_BTC", //pair
            "open":"0.03528700", //open price
            "high":"0.03587400", //high price
            "low":"0.03389300",//low price
            "last":"0.03428700",//last price
            "vol":"18484.75200000"//volume(in the last 24hours sliding window)
        }
    ]
}
```

### 3, Depth

> Description

Get the market depth for specific market.

> URL

/api/v2/depth

> Http Method

GET

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| pair | string | true  | IDAX supports trade pairs. Returns a specific trade against the market when specifying a pair,Returns all trading to market prices without specifying pair;|
| size | integer | true | how many price level should be response. must be between 1 - 200|
| merge| integer | true | price decimal price should be merged. |

> Request

```bash
curl https://openapi.idax.mn/api/v2/depth?pair=ETH_BTC&size=5&merge=8
```

> Response

```json
{
    "code": 10000,
    "msg": "request success",
    "asks": [ //ask depth
        [
            "0.03434400",
            "0.31100000"
        ],
        [
            "0.03436300",
            "0.18900000"
        ],
        [
            "0.03436400",
            "1.01900000"
        ],
        [
            "0.03437100",
            "0.17200000"
        ],
        [
            "0.03437400", //sell price
            "0.43400000"  //sell qty
        ]
    ],
    "bids": [ //bid depth
        [
            "0.03427400", //buy price
            "0.21100000"  //buy qty
        ],
        [
            "0.03427100",
            "22.05400000"
        ],
        [
            "0.03427000",
            "0.44700000"
        ],
        [
            "0.03426800",
            "2.95100000"
        ],
        [
            "0.03426700",
            "0.94900000"
        ]
    ]
}
```

### 4, Trades

> Description

Get Recently 60 Trades

> URL

/api/v2/trades

> Http Method

GET

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| pair | string | true  | IDAX supports trade pairs. |

> Request

```bash
curl https://openapi.idax.mn/api/v2/trades?pair=ETH_BTC
```

> Response

```json
{
    "code": 10000,
    "msg": "request success",
    "trades": [
        {
            "timestamp": 1536322351000, //trade time
            "price": "0.03428800", //deal price
            "quantity": "1.19400000", //qty in base coin
            "id": "6ce36df8-c87a-4517-8a6f-a67affe0481b", //trade id
            "maker": "Sell" //deal direction Buy/Sell
        },
        {
            "timestamp": 1536322353000,
            "price": "0.03428800",
            "quantity": "0.97200000",
            "id": "a1870119-fdd1-484e-b86b-794554075326",
            "maker": "Buy"
        },
        {
            "timestamp": 1536322354000,
            "price": "0.03428700",
            "quantity": "0.96700000",
            "id": "c0e752c4-f20c-48ac-8cd9-c65823b1ea01",
            "maker": "Sell"
        },
        //.....
    ]
}
```

### 5, Kline

> Description

Get kline data

> URL

/api/v2/kline

> Http Method

GET

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| pair | string | true  | IDAX supports trade pairs. |
| period | string | true | 1min,5min,15min,30min,1hour,2hour,4hour,6hour,12hour,1day,1week|
| size | integer | false | specify data size to be acquired |
| since | long | false | timestamp(eg:1417536000000). data after the timestamp will be returned|

> Request

```bash
curl https://openapi.idax.mn/api/v2/kline?pair=ETH_BTC&period=1min
```

> Response

approximately 2000 pieces of data are returned each cycle

```json
{
    "code": 10000,
    "msg": "request success",
    "kline": [
        [
            1536323160000, //timestamp
            "0.03449400", //open price
            "0.03449400", //high price
            "0.03449400", //low price
            "0.03449400", //close price
            "0.71900000"  //volume
        ],
        [
            1536323220000,
            "0.03448500",
            "0.03448500",
            "0.03448500",
            "0.03448500",
            "1.48500000"
        ],
        //...
    ]
}
```

### 6， Account Info

> Description

Get account info

> URL

/api/v2/userinfo

> Http Method

POST

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| key | string | true  | apiKey of the user. |
| timestamp | long | true | Request timestamp (valid for 3 minutes)|
| sign | string | true | signature of request parameters |

> Request

```bash
curl -H "Content-Type: application/json" -x POST https://openapi.idax.mn/api/v2/userinfo --data '{"key":"VmhrcrQEF3ATxV2JtVMEH4dFpGEmYzixOL4VrvAeR2COXtc9pzXbvFV1jLbFXEQf","timestamp":1536323160000,"sign":"c69d8ec9e274dd20126972b2dfaedc8c74cf06fbb19f968eedcc0a300a95b9f6"}'
```

> Response

```json
{
    "code":10000,
    "msg":"request success",
    "total": { //total fund
        "BTC": "0",
        "ETH": "0"
    },
    "free": {  //available fund
        "BTC": "0",
        "ETH": "0"
    },
    "freezed": { //frozen fund
        "BTC": "0",
        "ETH": "0"
    }
}
```

### 7, Place Orders

> Description

Create new order

> URL

/api/v2/placeOrder

> Http Method 

POST

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| key | string | true  | apiKey of the user. |
| pair | string | true | IDAX supports trade pairs|
| orderType | string | true | order type: (limit/market) |
| orderSide | string | true | order side:(buy/sell) |
| price | decimal | true | order price |
| amount | decimal | true | order qty |
| timestamp | long | true | Request timestamp (valid for 3 minutes) |
| sign | string | true | signature of request parameters |

> Request

```bash
curl -H "Content-Type: application/json" -X POST "https://openapi.idax.mn/api/v2/placeOrder" --data '{"amount": 1.05,"key": "VmhrcrQEF3ATxV2JtVMEH4dFpGEmYzixOL4VrvAeR2COXtc9pzXbvFV1jLbFXEQf","orderSide": "buy","orderType": "limit","pair": "ETH_BTC","price": 0.034775,"sign": "c69d8ec9e274dd20126972b2dfaedc8c74cf06fbb19f968eedcc0a300a95b9f6","timestamp":1532522823039}'
```

> Response

```json
{
    "code":10000,
    "msg":"request success",
    "orderId":123456789 --order ID
}
```

### 8, Trade History

> Description

get trade history for specific pairs

> URL

/api/v2/tradesHistory

> Http Method

POST

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| key | string | true | apiKey of the user |
| pair | string | true | IDAX supports trade pairs |
| since | long | true|get 600 pieces of data starting from the given tid (Required)|
| timestamp | long | true | Request timestamp (valid for 3 minutes) |
| sign | string | true | signature of request parameters |

> Request

```bash
TODO
```

> Response

```json
{
    "code":10000,
    "msg":"request success",
    "trades":[{
        "timestamp": 1367130137, // trade time
        "price": "787.71", // deal price
        "quantity": "0.003", // qty in base coin
        "id": 2304331234, // trade id
        "maker":"buy" // buy/sell
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

### 9, Cancel Order

> Description

Cancel orders (Support multiple orders per request)

> URL

/api/v2/cancelOrder

> Http Method

POST

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| key | string | true | apiKey of the user |
| orderId | string | true | order ID (multiple orders are separated by a comma ',', Max of 5 orders are allowed per request)|
| timestamp | long | true |Request timestamp (valid for 3 minutes)|
| sign | string | true | signature of request parameters|

> Request

```bash
TODO
```

> Response

```json
{
    "code":10000,
    "msg":"request success",
    "accepted":"123456789,123456000" // IDs(Accepted request for cancellation of order)
}
```

### 10, Order Info

> Description

Get Order Info

> URL

/api/v2/orderInfo

> Http Method

POST

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| key| string | true | apiKey of the user |
| pair | string | true | IDAX supports trade pairs|
| orderId | long | true |if order_id is -1, then return all unfilled orders, otherwise return the order specified|
| pageIndex | int | true | current page number |
| pageSize | int | true | number of orders returned per page, maximum 100 |
| timestamp | long | true | request timestamp (valid for 3 minutes) |
| sign| string | true | signature of request parameters|

> Request

```bash
TODO
```

> Response

```json
{
    "code":10000,
    "msg":"request success",
    "orders": [
        {
            "quantity": "0.1", // order quantity
            "avgPrice": "0", // average transaction price
            "timestamp": 1418008467000,// order time
            "dealQuantity": "0", // filled quantity
            "orderId": 10000591, // order ID
            "price": "500", // order price
            "orderState":1, // 1 = unfilled,2 = partially filled, 9 = fully filled, 19 = cancelled
            "pair": "ETH_BTC",
            "orderSide":"buy" // buy/sell
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

### 11, Order List

> Description

Get Order Information in Batch

> URL

/api/v2/orderList

> Http Method

POST

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| key | string | true | apiKey of the user |
| pair | string | true | IDAX supports trade pairs |
| orderId | string | true | order ID (multiple orders are separated by ',', 50 orders at most are allowed per request)|
| timestamp | long | true | Request timestamp (valid for 3 minutes) |
| sign | string | true | signature of request parameters|

> Request

```bash
TODO
```

> Response

```json
{
    "code":10000,
    "msg":"request success",
    "orders": [
        {
            "quantity": "0.1", //order quantity
            "avgPrice": "0", // average transaction price
            "timestamp": 1418008467000, // order time
            "dealQuantity": "0", // filled quantity
            "orderId": 10000591, // order ID
            "price": "500", // order price
            "orderState":1, // 1 = unfilled,2 = partially filled, 9 = fully filled, 19 = cancelled
            "pair": "ETH_BTC",
            "orderSide":"buy" // buy/sell
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

### 12, Order History

> Description

Get historical order information and return information only for the last two days

> URL

/api/v2/orderHistory  

> Http Method

POST

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| key | string | true | apiKey of the user |
| pair | string | true | IDAX supports trade pairs |
| orderState | integer | true | query status: 0 for all orders,query status: 0 for unfilled orders, 1 for filled orders  (only the data of the last two days are returned) |
| currentPage | integer | true | current page number |
| pageLength | integer | true | number of orders returned per page, maximum 100 |
| timestamp | long | true | request timestamp (valid for 3 minutes) |
| sign | string | true | signature of request parameters |

> Request

```bash

```

> Response

```json
{
        "code":10000,
        "msg":"request success",
        "currentPage": 1, // current page number
        "orders": // detailed order information
        [
            {
                "quantity": "0.2", // order quantity
                "avgPrice": "0", // average transaction price
                "timestamp": 1417417957000, // order time
                "dealQuantity": "0", // filled quantity
                "orderId": 10000724, // order ID
                "price": "0.1", // order price
                "orderState":1, // orderState: 1 = unfilled,2 = partially filled, 9 = fully filled, 19 = cancelled
                "pair": "ETH_BTC",
                "orderSide":"buy" // buy/sell
            }
        ],
        "pageLength": 1, // number of orders per page
        "total": 3 // The total number of records
}
```
### 13, PairLimits

> Description

Gets the maximum, minimum, price, and quantity of the supported transaction pairs.

> URL

/api/v2/pairLimits

> Http Method

GET

> Request

```bash
curl https://openapi.idax.mn/api/v2/pairLimits?pair=ETH_BTC
```

> Response

```json
{
    "code": 10000,
    "msg": "request success",
    "pairRuleVo": [{
        "pairName": "ETH_BTC",  //pair
        "maxAmount": "1000000000000.00000000",  //max amount
        "minAmount": "0.00100000", //min amount
        "priceDecimalPlace": 6,  // price decimal 
        "qtyDecimalPlace": 3  //quantity decimal
    }]
}
```

## 7, FAQ

> 1, Access restrictions

Answer: Each IP can send maximum of 10 https requests within 1 second. If the 10 limit is exceeded, the system will automatically block the IP for one hour. After that hour, the IP will be automatically unfrozen.

> 2, Server returns 10000 error code

Answer:All requests go over https protocol, The field 'contentType' in request header should be: 'application/x-www-form-urlencoded'.

## 8, The error codes

| Code | Description |
| ------|------|
| 10000 |    Successful request processing |
| 10001 |    System exception/server busy |
|10002|    This IP is not allowed to access|
|10003|    Only support https request|
|10004|    Request frequency too high to exceed the limit allowed|
|10005|    Non-available API|
|10006|    Required field, can not be null|
|10007|    'Api_key' does not exist|
|10008|    'SecretKey' does not exist|
|10009|    Signature does not match|
|10010|    API authorization error|
|10011|    Illegal parameter|
|10012|    IDAX not supports trade pairs|
|10013|    Insufficient funds|
|10014|    Insufficient coins balance|
|10015|    The Trading volume is less than the minimum requirement|
|10016|    The transaction amount is less than the minimum requirement|
|10017|    Trading volume is greater than the maximum volume|
|10018|    The transaction amount is greater than the maximum requirement|
|10019|    The deviation between the order price and the latest transaction price is too large|
|10020|    the Decimal places of Order number over limit|
|10021|    the Decimal places of Order price over limit|
|10022|    Order does not exist|
|10023|    order type is wrong|
|10024|    Wrong buying and selling direction|
|10025|    Order failed|
|10026|    Withdrawal failure|
|10027|    User account frozen|
|10028|    Account does not exist|
|10029|    No trading market information|
|10030|    No latest market information|
|10031|    No market depth information|
|10032|    incorrect ID|
|10033|    No chart type|
|10999|    According to relevant laws, your country or region cannot use this function.|
