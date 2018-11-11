# WebSocket Service 
***


## Service address

```
wss://openws.idax.pro/ws
```

## Request Data Format
```
{'event':'addChannel','channel':'channelValue','parameters':{'apiKey':'value1',timestamp:'value2','sign':'value3'}}
```

* event: addChannel(register request)/removeChannel(unregister request)/clearChannel(unregister all request)  
* channel: IDAX provided data type   
* parameters: optional field, reserved for trading services, including ` ` ` apiKey ` ` ` for users apply for apiKey ` ` ` sign ` ` ` for signature string

### Single registration
```
websocket.send("{'event':'addChannel','channel':'idax_sub_eth_btc_ticker'}")
```
### Bulk registration
```
websocket.send("[
  {'event':'addChannel','channel':'idax_sub_eth_btc_ticker'},
  {'event':'addChannel','channel':'idax_sub_eth_btc_depth'},
  {'event':'addChannel','channel':'idax_sub_eth_btc_trades'}
]");
```


### Server response

The return data format is：
```
[{"channel":"channel","code":"","data":[{}]}, {"channel":"channel","code":"","data":[{}]}]   
```
* channel:The requested data type
* code: "00000": register/unregister success, others register/unregister failure  
* data：returned data

## Push process description

* In order to guarantee the timeliness of push and reduce traffic, ticker and depth will push data only when data changes. Trading record is the incremental data generated from the last push to this push.

* Every time the subscription is successful, the data will be pushed once. The data contains the current data, and the code indicates whether the subscription is successful or not

* Each time you unsubscribe, the server will push a message with code success

* idax_sub_X_depth_Y is pushing one time per second, one full push at a time

* How to determine if the connection is broken?

>IDAX solves this problem through the heartbeat mechanism. The client can send a heartbeat data: ` ` ` {" event ":" ping "} ` ` `, the server will respond to the client: ` ` ` {" event ":" pong "} ` ` ` This indicates that the client and server are connected normally. If the client does not receive heartbeat data from the server response, the client needs to re-establish the connection.

# WebSocket API reference
***

## idax_sub_X_ticker  Subscription quotation data

### request demo
~~~
websocket.send("{'event':'addChannel','channel':'idax_sub_X_ticker'}");
~~~

* The X value is: trade pairs supported by IDAX (listening is case insensitive, but message push is case)


### Request
~~~
{'event':'addChannel','channel':'idax_sub_eth_btc_ticker'}
~~~

### Response
~~~
{
    "channel":"idax_sub_eth_btc_ticker",
    "data":[{
      "open":"2466",
      "high":"2555",
      "low":"2466",
      "last":"2478.51",
      "timestamp":1411718074965,
      "vol":"49020.30"
    }]
}
~~~

## response description

* open: The opening price

* high: The highest price

* low: The lowest price

* last: The Latest price

* timestamp：The timestamp

* vol: Volume (last 24 hours)

***

## idax_sub_X_depth  Subscribe to market depth (200 incremental data return)

### request demo
```
websocket.send("{'event':'addChannel','channel':'idax_sub_X_depth'}");
```

* The X value is: trade pairs supported by IDAX

### Request
```
{'event':'addChannel','channel':'idax_sub_eth_btc_depth'}
```

### Response
```
{
    "channel":"idax_sub_eth_btc_depth",
    "data":[{
        "bids":[
            ["2473.88","2.025"],
            ["2473.5","2.4"],
            ["2470","12.203"]
        ],
        "asks":[
            ["2484","17.234"],
            ["2483.01","6"],
            ["2482.88","3"]
        ],
        "timestamp":1411718972024
    }]
}
```

### response description

* Used to describe：
After the first return of the total data (<=200), the first return of the data is performed according to the server data in the following three operations
Delete (when the quantity is 0)
Modification (same price, different quantity)
Increase (price does not exist)

* bids([string, string]):bids data of depth

* asks([string, string]):asks data of depth

* timestamp(string):Server timestamp

***

## idax_sub_X_depth_Y  Subscription market depth (1 push per second)

### request demo
```
websocket.send("{'event':'addChannel','channel':'idax_sub_X_depth_Y'}");
```

* The X value is: trade pairs supported by IDAX (lower case)

* The Y value is: 5, 10, 20, 50(number of depth bars obtained)

### Request
```
{'event':'addChannel','channel':'idax_sub_eth_btc_depth_20'}
```
### Response
```
{
    "channel":"idax_sub_eth_btc_depth_20",
    "data":[{
        "bids":[
            ["2473.88","2.025"],
            ["2473.5","2.4"],
            ["2470","12.203"]
        ],
        "asks":[
            ["2484","17.234"],
            ["2483.01","6"],
            ["2482.88","3"]
        ],
        "timestamp":1411718972024
    }]
}
```

### response description

* bids([string, string]):bids data of depth
* asks([string, string]):asks data of depth
* timestamp(long):Server timestamp


## idax_sub_X_trades  Subscription transaction record (incremental data return)

### request demo
```
websocket.send("{'event':'addChannel','channel':'idax_sub_X_trades'}");
```

* The X value is: trade pairs supported by IDAX

### Request
```
{'event':'addChannel','channel':'idax_sub_eth_btc_trades'}
```
### Response
```
{
    "channel":"idax_sub_eth_btc_trades",
    "data":[["1001","2463.86","0.052",1411718972024,"buy"]]
}
```
### response description

[Transaction number, price, volume, Clinch a deal the time, Clinch a deal the type（buy|sell）]
***


## idax_sub_X_kline_Y  Subscribe K line data

### request demo
```
websocket.send("{'event':'addChannel','channel':'idax_sub_X_kline_Y'}");
```
* The X value is: trade pairs supported by IDAX (lower case)

* The Y value is: 1min, 5min, 15min, 30min, 1hour, 2hour, 4hour, 6hour, 12hour, 1day, 1week

### Request
```
{'event':'addChannel','channel':'idax_sub_eth_btc_kline_1min'}
```
### Response
```
{
    "channel":"idax_sub_eth_btc_kline_1min",
    "data":[
        ["1490337840000","995.37","996.75","995.36","996.75","9.112"],
        ["1490337840000","995.37","996.75","995.36","996.75","9.112"]
    ]
}
```
### response description

[The timestamp, The opening, The highest, The lowest price, The closing price, volume]
***
# Common problems with the WebSocket API

## How to determine if the server and client connections are normal?
A: IDAX solves this problem through the heartbeat mechanism. The client sends the heart rate data: ` ` ` {' event ':' ping '} ` ` `, the server will respond to the client: ` ` ` {" event ":" pong "} ` ` ` to show the client and the server to maintain normal connection. If the client does not receive heartbeat data from the server response, the client needs to re-establish the connection.
