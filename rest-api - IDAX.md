# Public Rest API for IDAX (2018-03-13)
# General API Information
* The base endpoint is: **https://openapi.idax.mn**
* All endpoints return either a JSON object or array.

## General endpoints

## Sign Sketch ##

### Main Parameter ###

```
[key]：
[secret]：
[timestamp]：Unix（毫秒），允许时间差在120秒,即120 * 1000毫秒

```
### Generation method ###

#### 将key、timestamp、以及其它参数的键值以升序排列，用&符号隔开转为字符串。将字符串和secret以(HMAC)SHA256哈希函数加密生成sign。 ####

### Test connectivity
```
GET /api/v1/ping
```
Test connectivity to the Rest API.

**Weight:**
1

**Parameters:**
NONE

**Response:**
```javascript
{
    "success": true,
    "message":"success"
}
```

### Test verifyapi
```
GET /api/v1/verifyapi
```
Test connectivity to the Rest API.

**Weight:**
1

**Parameters:**
NONE

**Response:**
```javascript
{
    "success": true,
    "message":"success"
}
```

### Check server time
```
GET /api/v1/time
```
Test connectivity to the Rest API and get the current server time.

**Weight:**
1

**Parameters:**
NONE

**Response:**
```javascript
{
  "time": 1499827319559
}
```



## Market Data endpoints
### Order book
```
GET /api/v1/depth
```

**Weight:**
Adjusted based on the limit:


Limit | Weight
------------ | ------------
5, 10, 20, 50, 100 | 1
500 | 5
1000 | 10

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
pair | STRING | YES | 
limit | INT | NO | Default 100; max 1000. Valid limits:[5, 10, 20, 50, 100, 500, 1000]
dec | INT | NO | Default value is 8

**Caution:** setting limit=0 can return a lot of data.

**Response:**
```javascript
{
  "data": [
    {
      "orderSide": 1,
      "price": 1.0,
      "qty": 2.0
    }
  ],
  "success": true,
  "message": "sample string 2"
}
```

### Recent trades list
```
GET /api/v1/trades
```
Get recent trades (up to last 500).

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
pair | STRING | YES | 
limit | INT | NO | Default 100; max 1000. Valid limits:[5, 10, 20, 50, 100, 500, 1000]
key | STRING | YES |
timestamp | INT | YES
sign | STRING | YES |

**Caution:** setting limit=0 can return a lot of data.

**Response:**
```javascript
{
  "data": [
    {
      "pairName": "sample string 1",
      "time": "sample string 2",
      "orderSide": 3,
      "price": 4.0,
      "qty": 5.0,
      "fee":1.0
    },
  ],
  "success": true,
  "message": "sample string 2"
}
```

### Recent kline
```
GET /api/v1/kline
```
Get recent trades (up to last 500).

**Weight:**
1


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
pair | STRING | YES | 
period | STRING | NO | Default value is 1D;[1T,5T,15T,30T,1H,2H,4H,6H,12H,1D,1W]

**Caution:** setting limit=0 can return a lot of data.

**Response:**
```javascript
{
  "data": [
    {
      "p": "sample string 1", //period
      "t": "sample string 2", //time
      "o": 3.0,               //open
      "h": 4.0,               //high
      "c": 5.0,               //close
      "l": 6.0,               //low
      "v": 7.0                //Volume
    }
  ]
}
```
### Recent balances
```
GET /api/v1/balances
```
Get recent trades (up to last 500).

**Weight:**
1


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
key | STRING | YES | 
timestamp | INT | YES |
sign | STRING | YES |

**Caution:** setting limit=0 can return a lot of data.

**Response:**
```javascript
{
  "data": [
    {
      "coinId": "sample string 1",
      "coinCode": "sample string 2",
      "available": 3.0,
      "frozen": 4.0,
      "isDepositEnabled": true,
      "isWithdrawEnabled": true
    }
  ],
  "success": true,
  "message": "sample string 2"
}
```


## Account endpoints
### New order  (TRADE)
```
POST /api/v1/createorder  (HMAC SHA256)
```
Send in a new order.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
orderSide | INT | YES | 1:Buy  2:Sell
orderType | INT | YES | 1:Limit
pair | STRING | YES | 
price | DECIMAL | YES |
amount | DECIMAL | YES | 
key | STRING | YES |
timestamp | INT | YES |
sign | STRING | YES |

**Response:**
```javascript
{
    "success": true,
    "message":"success"
}
```

### Query myOrder (USER_DATA)
```
GET /api/v1/myOrders (HMAC SHA256)
```
Check an order's status.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
top | int | YES |
key | STRING | YES |
timestamp | INT | YES |
sign | STRING | YES |

**Response:**
```javascript
{
  "data": [
    {
      "orderId": "sample string 1",
      "orderSide": 2,
      "pairName": "sample string 3",
      "price": 4.0,
      "total": 5.0,
      "filledQty": 6.0,
      "time": "sample string 7"
    }
  ]
}
```

### Cancel cancelorder (TRADE)
```
DELETE /api/v1/cancelorder  (HMAC SHA256)
```
Cancel an active order.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
orderId | STRING | YES |
key | STRING | YES |
timestamp | INT | YES |
sign | STRING | YES |

**Response:**
```javascript
{
    "success": true,
    "message":"success"
}
```

### Account myTrades list (USER_DATA)
```
GET /api/v1/myTrades  (HMAC SHA256)
```
Get trades for a specific account and symbol.

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
top | STRING | YES |
key | STRING | YES |
timestamp | INT | YES |
sign | STRING | YES |

**Response:**
```javascript
[
  {
      "pairName": "sample string 1",
      "time": "sample string 2",
      "orderSide": 3,
      "price": 4.0,
      "qty": 5.0
  }
]
```
# Detailed Stream information
## Aggregate Trade Streams
The Aggregate Trade Streams push trade information that is aggregated for a single taker order.

**wss://openapi.idax.mn/api/v1/aggtrade@ETH_BTC**

**Stream Name:** aggTrade@\<pair\>

**Payload:**
```javascript
{
  "time": "",           // Trade time
  "orderSide": 1,       // OrderSide
  "price": "ETH_BTC",   // Price
  "qty": "0.001",       // Quantity
}
```
```
