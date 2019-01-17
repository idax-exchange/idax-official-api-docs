# Overview

IDAX provide powerful and robust APIs to help user write program to automatic the tradings. With this guide you can learn how to use APIs.

Interface list:

|Interface data type| Request  method   | type |Need to verify signature|describe|
| ------------------| ----------------- | ----- |----- |-----------------------|
| system information|[/api/v2/time](#1-server-time) | GET  |  N   | Get Server timestamp |
| trade             |[/api/v2/placeOrder](#2-place-orders) | POST  |  Y   | Create new order      |
| trade             |[/api/v2/cancelOrder](#3-cancel-order) | POST  |  Y   | Cancel orders        |
| Order information |[/api/v2/orderInfo](#4-order-info) | POST  |  Y   | Get Order Info |
| Order information |[/api/v2/orderList](#5-order-list) | POST  |  Y   | The interface suspends service|
| Order information |[/api/v2/orderHistory](#6-order-history) | POST  |  Y   |Order information within the last 24 hours|
| Order information |[/api/v2/beforeOrderHistory](#7-before-order-history) | POST  |  Y   |Query 24 hours outside history commission|
| trade information |[/api/v2/trades](#8-trades) | GET  |  N   | Get Recently  Trades|
| trade information |[/api/v2/tradesHistory](#9-trade-history) | POST  |  Y   | The interface suspends service|
| trade information |[/api/v2/myTrades](#10-mytrades) | POST  |  Y   |Get my historical trading information|
| Account information|[/api/v2/userinfo](#11-account-info) | POST  |  Y   |Get account info|
| Market quotation  |[/api/v2/ticker](#12-ticker-price) | GET  |  N   | Get the price of specific ticker |
| Market quotation  |[/api/v2/depth](#13-depth) | GET  |  N   | Get the market depth for specific market.  |
| Market quotation  |[/api/v2/kline](#14-kline) | GET |  N   | Get kline data|
| trade variety information|[/api/v2/pairs](#15-pairs) | GET  |  N   | All trading pairs supported by exchanges|
| trade variety information|[/api/v2/pairLimits](#16-pairlimits) | GET  |  N   |Gets the maximum, minimum, price, and quantity of the supported transaction pairs|
| system information|[/api/v2/getSign](#17-getsign) | GET  |  N   | Get sign |

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

You can get the apiKey and secret key from idax website : https://www.idax.pro.

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
curl -H "Content-Type: application/json" -X POST "https://openapi.idax.pro/api/v2/placeOrder" --data '{"amount": 1.05,"key": "VmhrcrQEF3ATxV2JtVMEH4dFpGEmYzixOL4VrvAeR2COXtc9pzXbvFV1jLbFXEQf","orderSide": "buy","orderType": "limit","pair": "ETH_BTC","price": 0.034775,"sign": "c69d8ec9e274dd20126972b2dfaedc8c74cf06fbb19f968eedcc0a300a95b9f6","timestamp":1532522823039}'
```

## 5, Rest API

* The base endpoint is : [https://openapi.idax.pro/api/v2](https://openapi.idax.pro/api/v2)
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
curl https://openapi.idax.pro/api/v2/time
```

> Response

```json
{
    "code":10000,
    "msg":"Successful request processing",
    "timestamp":1536318118202 --server timestamp
}
```

### 2, Place Orders

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
curl -H "Content-Type: application/json" -X POST "https://openapi.idax.pro/api/v2/placeOrder" --data '{"amount": 1.05,"key": "VmhrcrQEF3ATxV2JtVMEH4dFpGEmYzixOL4VrvAeR2COXtc9pzXbvFV1jLbFXEQf","orderSide": "buy","orderType": "limit","pair": "ETH_BTC","price": 0.034775,"sign": "c69d8ec9e274dd20126972b2dfaedc8c74cf06fbb19f968eedcc0a300a95b9f6","timestamp":1532522823039}'
```

> Response

```json
{
    "code":10000,
    "msg":"Successful request processing",
    "orderId":"2000000000008832432"  //type String  order ID
}
```
### 3, Cancel Order

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
    "msg":"Successful request processing",
    "accepted":"123456789,123456000" // IDs(Accepted request for cancellation of order)
}
```

### 4, Order Info

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
| orderId | string or long | true |if order_id is -1, then return all unfilled orders, otherwise return the order specified|
| pageIndex | int | true | current page number |
| pageSize | int | true | number of orders returned per page |
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
    "msg":"Successful request processing",
    "orders": [
        {
            "quantity": "0.1", // order quantity
            "avgPrice": "0", // average transaction price
            "timestamp": 1418008467000,// order time
            "dealQuantity": "0", // filled quantity
            "orderId": 10000591, // order ID
            "price": "500", // order price
            "orderState":1, // 1 = unfilled,2 = partially filled, 9 = fully filled, 19 = cancelled
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
            "orderSide":"sell"
        }],
        "total": 1
}
```

### 5, Order List

> Description

The interface suspends service

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
    "msg":"Successful request processing",
    "orders": [
        {
            "quantity": "0.1", //order quantity
            "avgPrice": "0", // average transaction price
            "timestamp": 1418008467000, // order time
            "dealQuantity": "0", // filled quantity
            "orderId": 10000591, // order ID
            "price": "500", // order price
            "orderState":1, // 1 = unfilled,2 = partially filled, 9 = fully filled, 19 = cancelled
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
            "orderSide":"sell"
        }]
}
```

### 6, Order History

> Description

Order information within the last 24 hours

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
        "msg":"Successful request processing",
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

### 7, Before Order History

> Description

Query 24 hours outside history commission

> URL

/api/v2/beforeOrderHistory  

> Http Method

POST

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| key | string | true | apiKey of the user |
| pair | string | true | IDAX supports trade pairs |
| orderState | integer | true | query status: -1 for all orders,query status: 0 for unfilled orders, 1 for filled orders  |
| currentPage | integer | false | current page number |
| pageLength | integer | false | number of orders returned per page, maximum 100 |
| timestamp | long | true | request timestamp (valid for 3 minutes) |
| sign | string | true | signature of request parameters |
| orderSide | int | false | 0 all 1 buy  2 sell |
| startTime | long | false | startTime |
| endTime | long | false | startTime |

> Request

```bash

```

> Response

```json
{
        "code":10000,
        "msg":"Successful request processing",
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

### 8, Trades

> Description

The default total does not return one, and returns up to 2000 at a time.

> URL

/api/v2/trades

> Http Method

GET

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| pair | string | true  | IDAX supports trade pairs. |
| total | int | false  | Number of items; no default display 1 |

> Request

```bash
curl https://openapi.idax.pro/api/v2/trades?pair=ETH_BTC&total=3
```

> Response

```json
{
    "code": 10000,
    "msg": "Successful request processing",
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

### 9, Trade History

> Description

The interface suspends service

> URL

/api/v2/tradesHistory

> Http Method

POST

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| key | string | true | apiKey of the user |
| pair | string | true | IDAX supports trade pairs |
| since | long | false|Get the latest 600 pieces of data from a given ID(Since fetches the returned trade ID) |
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
    "msg":"Successful request processing",
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

### 10, MyTrades

> Description

Get my historical trading information

> URL

/api/v2/myTrades  

> Http Method

POST

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| key | string | true | apiKey of the user |
| pair | string | false | IDAX supports trade pairs |
| orderSide | string | false | buy，sell |
| currentPage | integer | true | current page number |
| pageLength | integer | true | number of trade returned per page, maximum 1000 |
| startDate | long | false | start date and timestamp (Millisecond) |
| endDate   | long | false | end date and timestamp (Millisecond) |
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
    "trades":[{
        "timestamp": 1367130137,
        "price": "787.71",    // order price
        "quantity": "0.003", //order quantity
        "pair": "ETH_BTC",
        "maker":"buy"  // buy/sell
    },
    {
        "timestamp": 1367130137,
        "price": "787.71",
        "quantity": "0.003",
        "pair": "EOS_BTC",
        "maker":"sell" 
    }],
     "total": 2 // The total number of records
}
```

### 11, Account Info

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
curl -H "Content-Type: application/json" -x POST https://openapi.idax.pro/api/v2/userinfo --data '{"key":"VmhrcrQEF3ATxV2JtVMEH4dFpGEmYzixOL4VrvAeR2COXtc9pzXbvFV1jLbFXEQf","timestamp":1536323160000,"sign":"c69d8ec9e274dd20126972b2dfaedc8c74cf06fbb19f968eedcc0a300a95b9f6"}'
```

> Response

```json
{
    "code":10000,
    "msg":"Successful request processing",
    "total": { //total fund
        "BTC": "0",
        "ETH": "0",
        "USDT": "0"
    },
    "free": {  //available fund
        "BTC": "0",
        "ETH": "0",
        "USDT": "0"
    },
    "freezed": { //frozen fund
        "BTC": "0",
        "ETH": "0",
        "USDT": "0"
    }
}
```


### 12, Ticker Price

> Description

Get the price of specific ticker.

> URL

/api/v2/ticker

> Http Method

GET
> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| pair | string | false  | IDAX supports trade pairs. |

> Request

```bash
curl https://openapi.idax.pro/api/v2/ticker?pair=ETH_BTC
```

> Response

```json
{
    "code":10000,
    "msg":"Successful request processing",
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

### 13, Depth

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
| size | integer | false | how many price level should be response. must be between 1 - 200|
| merge| integer | false | price decimal price should be merged. |

> Request

```bash
curl https://openapi.idax.pro/api/v2/depth?pair=ETH_BTC&size=5&merge=8
```

> Response

```json
{
    "code": 10000,
    "msg": "Successful request processing",
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

### 14, Kline

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
| since | long | false | timestamp(eg:1417536000000). Data before returning timestamp|

> Request

```bash
curl https://openapi.idax.pro/api/v2/kline?pair=ETH_BTC&period=1min
```

> Response

approximately 2000 pieces of data are returned each cycle

```json
{
    "code": 10000,
    "msg": "Successful request processing",
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

### 15, Pairs

> Description

All trading pairs supported by exchanges

> URL

/api/v2/pairs

> Http Method

GET

> Request

```bash
curl https://openapi.idax.pro/api/v2/pairs
```

> Response

```json
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
        "PAT_BTC", "CAPP_ETH", "APIS_BTC", "ETH_BTC", "ARN_BTC", "FIC_ETH", "APT_ETH"] //pair name
} 
```

### 16, PairLimits

> Description

Gets the maximum, minimum, price, and quantity of the supported transaction pairs.

> URL

/api/v2/pairLimits

> Http Method

GET

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| pair | string | false  | IDAX supports trade pairs. |

> Request

```bash
curl https://openapi.idax.pro/api/v2/pairLimits?pair=ETH_BTC
```

> Response

```json
{
    "code": 10000,
    "msg": "Successful request processing",
    "pairRuleVo": [{
        "pairName": "ETH_BTC",  //pair
        "maxAmount": "1000000000000.00000000",  //max amount
        "minAmount": "0.00100000", //min amount
        "priceDecimalPlace": 6,  // price decimal 
        "qtyDecimalPlace": 3  //quantity decimal
    }]
}
```
### 17, GetSign

> Description

Developers use getsign to verify that the signature algorithm is correct. 
The secret is fixed as: otcyACN3wfloCLpAHGcf6jIdHErASs4m7Rbi4ei0QgQRI7TwxhF54hJeV905lnkd.

> URL

/api/v2/getSign

> Http Method

GET

> Parameters

| name | type | required | description |
|------|------|----------|-------------|
| needSignature | string | true  | The string to be signed must be in JSON format |

> Request

```bash
curl https://openapi.idax.pro/api/v2/getSign?needSignature={"amount":10,"key":"otcyACN3wfloCLpAHGcf6jIdHErASs4m7Rbi4ei0QgQRI7TwxhF54hJeV905lnkd","orderSide":"buy","orderType":"limit","pair":"ETH_BTC","price":0,"sign":"47c63e8c8dfac216e3e2182b0eb909a85e38b9c4aec4aaf9cb1f65c24bc52c63","timestamp":1545347383600}
```

> Response

```json
{
	"code": 10000,
	"msg": "Successful request processing",
	"sign": "907ab44a485727b604354da47acc6e54e1abeff73defcaa282f38c50b21877ec"
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
| 10002 |    Parameters can not be empty
| 10003 |    Illegal parameter
| 10004 |    Not supported pair
| 10005 |    The transaction is not supported
| 10006 |    order state is error
| 10007 |    No account information
| 10008 |    key does not exist
| 10009 |    Timestamp is empty
| 10010 |    Timestamp Expirated
| 10011 |    Sign is empty
| 10012 |    Ip is not on the white list of requests
| 10013 |    Signature mismatch
| 10014 |    Place Order failed
| 10015 |    Cancel  Order  failed
| 10016 |    The request interface is too frequent. Please try again later
| 10017 |    Exchanges do not support trading pair
| 10018 |    Currency price is empty
| 10019 |    A maximum of 5 orders can be revoked at a time
| 10020 |    orderId is empty
| 10021 |    secret is empty
| 101001|    Unlawful price input
| 101002|    Unlawful quantity input
| 101003|    Unlawful order side input
| 101004|    Coin Pair UnKnown
| 101005|    Trade CreateOrder Price Decimal Place Error
| 101006|    Trade CreateOrder Qty Decimal Place Error
| 101007|    Trade Amount Max
| 101008|    Trade Amount Min
| 101009|    Insufficient account balance
| 101010|    Available Amount Not Enough
| 101011|    OrderId NotExist
| 101012|    cancel OrderFail
| 101013|    Query  Floating Rate Fail
| 101014|    Buy Price Fail
| 101015|    Sell Price Fail
| 101016|    Create order maintenance
| 101017|    Query Setting Empty
| 101018|    Price input is empty
| 101019|    Amount input is empty
| 101020|    the order is finished
| 101021|    the order is cancel
| 101022|    Trade is not allowed to be traded on no shelf
| 101023|    This order is invalid

