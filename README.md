# 行情接口

## 测试能否联通

`GET /api/v1/ping`

**权重:**
1

**参数:**
NONE

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```

## 获取服务器时间

`GET /api/v1/time`

**权重:**
1

**参数:**
NONE

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": 1592387156596 // 当前的系统时间
}
```

## 获取此时的交易对信息

`GET /api/v1/optionInfo`

**权重:**
1

**参数:**
NONE

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "id": 1,
      "contractId": 1,
      "underlying": "BTCUSDT",          // 合约标的
      "quoteAsset": "USDT",             // 计价资产
      "symbol": "BTCUSDT-200730-9000-C",// 交易对名称
      "unit": 1,                        // 兑换比例，一张合约代表的标的数量
      "minQty": 1,                      // 合约标的最少交易量
      "side": "CALL",                   // 方向: CALL 多, PUT 空
      "leverage": 0,                    // 杠杆
      "strikePrice": 9000,              // 行权价格
      "expiryDate": 1593518400000       // 行权时间
    }
  ]
}
```

## 获取此时的限制信息和交易对信息

`GET /api/v1/exchangeInfo`

**权重:**
1

**参数:**
NONE

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "timezone": "UTC",                    // 服务器所用的时间区域
    "serverTime": 1592387337630,          // 当前的系统时间
    "optionContracts": [                  // 期权合约标的信息
      {
        "id": 1,
        "baseAsset": "BTC",               // 基准货币
        "quoteAsset": "USDT",             // 计价货币
        "underlying": "BTCUSDT",          // 期权合约标的名称
        "settleAsset": "USDT"             // 结算货币
      }
    ],
    "optionAssets": [                     // 期权资产信息
      {
        "id": 1,
        "name": "USDT"                    // 资产名称
      }
    ],
    "optionSymbols": [                    // 期权交易对信息
      {
        "id": 1,
        "contractId": 1,
        "underlying": "BTCUSDT",          // 合约标的
        "quoteAsset": "USDT",             // 计价资产
        "symbol": "BTCUSDT-200730-9000-C",// 交易对名称
        "unit": 1,                        // 兑换比例，一张合约代表的标的数量
        "minQty": 1,                      // 合约标的最少交易量
        "side": "CALL",                   // 方向: CALL 多, PUT 空
        "leverage": 0,                    // 杠杆
        "strikePrice": 9000,              // 行权价格
        "expiryDate": 1593518400000       // 行权时间
      }
    ]
  }
}
```

## 现货指数价格获取

`GET /api/v1/index`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
underlying | STRING | YES | 现货币种对（期权合约标的） | BTCUSDT

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "indexPrice": "9200" // 当前现货指数价格
  }
}
```

## 获取最新价格

`GET /api/v1/ticker`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | NO | 期权交易对 | BTCUSDT-200730-9000-C

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "symbol": "BTCUSDT-200730-9000-C",
      "priceChange": "-16.2038",        //24小时价格变动
      "priceChangePercent": "-0.0162",  //24小时价格变动百分比
      "lastPrice": "1000",              //最近一次成交价
      "lastQty": "1000",                //最近一次成交额
      "open": "1016.2038",              //24小时Open价格
      "high": "1016.2038",              //24小时High价格
      "low": "0",                       //24小时Low价格
      "volume": "5",                    //交易量
      "amount": null,                   //交易金额
      "openTime": 1592317127349,        //24小时内，第一笔交易的发生时间
      "closeTime": 1592380593516,       //24小时内，最后一笔交易的发生时间
      "firstTradeId": 1,                //首笔成交id
      "tradeCount": 5,                  //成交笔数
      "strikePrice": "9000"             //行权价
    }
  ]
}
```

## 获取最新标记价格

`GET /api/v1/mark`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | NO | 期权交易对 | BTCUSDT-200730-9000-C

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "symbol": "BTCUSDT-200730-9000-C",
      "markPrice": 1343.2883,       //标记价格
      "bidIV": "1.40000077",        //隐含波动率 买
      "askIV": "1.50000153",        //隐含波动率 卖
      "delta": 0.55937056,          //delta
      "theta": 3739.82509871,       //theta
      "gamma": 0.00010969,          //gamma
      "vega": 978.58874732,         //vega
      "highPriceLimit": 1618.241,   //当前最高价格限制
      "lowPriceLimit": 1068.3356    //当前最低价格限制
    }
  ]
}
```

## 深度信息

`GET /api/v1/depth`

**权重:**
5

**参数:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
limit | INT | NO | 默认值:100 最大值:1000.可选值:[5, 10, 20, 50, 100, 500, 1000] | 100

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "bids": [   // 买单
      [
        "1000", // 价格
        "0.9"   // 数量
      ]
    ],
    "asks": [   // 卖单
      [
        "1100", // 价格
        "0.1"   // 数量
      ]
    ]
  }
}
```

## K线数据

`GET /api/v1/klines`

**权重:**
5

**参数:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
interval | STRING | YES | 时间间隔 | 5m
startTime | LONG | NO | 起始时间 | 1592317127349
endTime | LONG | NO | 结束时间 | 1592318127349
limit | INT | NO | 记录条数 默认值:500 最大值:1500 | 500

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
      {
        "close": "1000",            // 收盘价(当前K线未结束的即为最新价)
        "closeTime": 1499644799999, // 收盘时间
        "tradeCount": 10,           // 成交笔数
        "high": "1100",             // 最高价
        "low": "900",               // 最低价
        "open": "950",              // 开盘价
        "openTime": 1499040000000,  // 开盘时间
        "takerAmount": "10000",     // 主动成交交易金额
        "takerVolume": "100",       // 主动成交交易量
        "interval": "5m",           // K线类型
        "volume": "100"             // 交易量
      }
  ]
}
```

## 期权近期成交

`GET /api/v1/trades`

**权重:**
5

**参数:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
limit | INT | NO | 交易记录条数 默认值:100 最大值:500 | 100

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "price": "1000",      // 成交价格
      "qty": "-0.1",        // 成交数量
      "quoteQty": "-100",   // 成交额
      "time": 1592449455993,// 时间
      "side": -1            // 成交方向（-1 卖，1 买）
    }
  ]
}
```

## 查询历史成交

`GET /api/v1/historicalTrades`

**权重:**
5

**参数:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
fromId | LONG | NO | 从哪一条成交id开始返回. 缺省返回最近的成交记录 | 1592317127349
limit | INT | NO | 交易记录条数 默认值:100 最大值:500 | 100

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "price": "1000",      // 成交价格
      "qty": "-0.1",        // 成交数量
      "quoteQty": "-100",   // 成交额
      "time": 1592449455993,// 时间
      "side": -1            // 成交方向（-1 卖，1 买）
    }
  ]
}
```


# Websocket 账户信息推送

## 生成 Listen Key (USER_STREAM)

`POST /api/v1/userDataStream  (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "listenKey": "eBjOsjYmu1WD6oZHCRpMeWm4UzMvQelhOc9yTD4c"
  }
}
```

## 延长 Listen Key 有效期 (USER_STREAM)

`PUT /api/v1/userDataStream`

有效期延长至本次调用后60分钟

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
listenKey | STRING | YES | Listen Key | eBjOsjYmu1WD6oZHCRpMeWm4UzMvQelhOc9yTD4c
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```

## 关闭 Listen Key (USER_STREAM)

`DELETE /api/v1/userDataStream  (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
recvWindow | STRING | YES | Listen Key | eBjOsjYmu1WD6oZHCRpMeWm4UzMvQelhOc9yTD4c
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```


# 账户和交易接口

## 账户资产信息(USER_DATA)

`GET /api/v1/account  (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "currency": "USDT",               // 资产类型
      "equity": 10094.44662,            // 账户权益
      "available": 8725.92524,          // 账户可用
      "orderMargin": 1084.52138,        // 委托保证金
      "positionMargin": 289.00138,      // 持仓保证金
      "unrealizedPNL": -5.00138,        // 未实现盈亏
      "maintMargin": 151.00138,         // 维持保证金
      "balance": 10099.448              // 账户余额
    }
  ]
}
```

## 资金划转(USER_DATA)

`POST /api/v1/transfer  (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
currency | STRING | YES | 资产类型 | USDT
type | ENUM | YES | IN: 现货账户向合约账户划转 OUT: 合约账户向现货账户划转 | IN
amount | DECIMAL | YES | 金额 | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": 4         // 划转记录ID
}
```

## 期权持仓信息(USER_DATA)

`GET /api/v1/position  (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | NO | 期权交易对 | BTCUSDT-200730-9000-C
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "entryPrice": 1000,               // 开仓均价
      "symbol": "BTCUSDT-200730-9000-C",// 期权交易对
      "side": "SHORT",                  // 持仓方向
      "leverage": 0,                    // 杠杆
      "quantity": -0.1,                 // 持仓数量（正数为多，负数为空）
      "reducibleQty": 0,                // 可平仓数量
      "markValue": 105.00138,           // 当前市值
      "autoReducePriority": 0,          // 减仓顺序
      "ror": -0.05,                     // 收益率
      "unrealizedPNL": -5.00138,        // 未实现盈亏
      "markPrice": 1050.0138,           // 标记价格
      "strikePrice": 9000,              // 行权价格
      "expiryDate": 1593511200000       // 行权时间
    }
  ]
}
```

## 账户资金流水(USER_DATA)

`POST /api/v1/bill  (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
currency | STRING | YES | 资产类型 | USDT
recordId | LONG | NO | 返回该recordId及之后的数据,缺省返回最近的数据 | 100000
startTime | LONG | NO | 起始时间 | 1593511200000
endTime | LONG | NO | 结束时间 | 1593512200000
limit | INT | NO | 返回的结果集数量 默认值:100 最大值:1000 | 100
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "id": 1125899906842624000,
      "currency": "USDT",           // 资产类型
      "amount": -0.552,             // 金额（正数代表流入，负数代表流出）
      "type": "FEE",                // 账单类型（手续费）
      "createDate": 1592449456000,  // 时间
    },
    {
      "id": 1125899906842624000,
      "currency": "USDT",           // 资产类型
      "amount": 100,                // 金额（正数代表流入，负数代表流出）
      "type": "CONTRACT",           // 账单类型（买卖合约）
      "createDate": 1592449456000,  // 时间
    },
    {
      "id": 1125899906842624000,
      "currency": "USDT",           // 资产类型
      "amount": 10000,              // 金额（正数代表流入，负数代表流出）
      "type": "TRANSFER",           // 账单类型（资金划转）
      "createDate": 1592448410000,  // 时间
    }
  ]
}
```

## 期权下单 (TRADE)

`POST /api/v1/order  (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
side | ENUM | YES | 买卖方向 SELL, BUY | BUY
type | ENUM | YES | 订单类型 LIMIT, MARKET | LIMIT
quantity | DECIMAL | YES | 下单数量 | 3
price | DECIMAL | NO | 委托价格 | 1000
timeInForce | ENUM | NO | 有效方法（默认GTC） | GTC
newOrderRespType | ENUM | NO | "ACK", "RESULT", 默认 "ACK" | ACK
clientOrderId | STRING | NO | 用户自定义的订单号，不可以重复出现在挂单中 | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

根据 order type的不同，某些参数强制要求，具体如下:

Type | 强制要求的参数
------------ | ------------ 
LIMIT | timeInForce, quantity, price
MARKET | quantity


>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": "4611875134427365377",        // 系统订单号
    "symbol": "BTCUSDT-200730-9000-C",  // 期权交易对
    "price": 100,                       // 委托价格
    "quantity": 1,                      // 委托数量
    "executedQty": 0,                   // 成交数量
    "fee": 0,                           // 手续费
    "side": "BUY",                      // 买卖方向
    "type": "LIMIT",                    // 订单类型
    "timeInForce": "GTC",               // 有效方法
    "createDate": 1592465880683,        // 委托时间
    "status": "ACCEPTED",               // 订单状态
    "avgPrice": 0,                      // 成交均价
    "source": "WEB",                    // 订单来源
    "clientOrderId": ""                 // 客户端订单ID
  }
}
```

## 期权撤销订单 (TRADE)

`DELETE /api/v1/order  (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
orderId | LONG | NO | 订单ID | 4611875134427365377
clientOrderId | STRING | NO | 用户自定义的订单号 | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

orderId 与 clientOrderId 必须至少发送一个

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": "4611875134427365377",        // 系统订单号
    "symbol": "BTCUSDT-200730-9000-C",  // 期权交易对
    "price": 100,                       // 委托价格
    "quantity": 1,                      // 委托数量
    "executedQty": 0,                   // 成交数量
    "fee": 0,                           // 手续费
    "side": "BUY",                      // 买卖方向
    "type": "LIMIT",                    // 订单类型
    "timeInForce": "GTC",               // 有效方法
    "createDate": 1592465880683,        // 委托时间
    "status": "CANCELLED",              // 订单状态
    "avgPrice": 0,                      // 成交均价
    "source": "WEB",                    // 订单来源
    "clientOrderId": ""                 // 客户端订单ID
  }
}
```

## 期权撤销全部订单 (TRADE)

`DELETE /api/v1/allOpenOrders (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```

## 期权查询订单 (TRADE)

`GET /api/v1/order (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
orderId | LONG | NO | 订单ID | 4611875134427365377
clientOrderId | STRING | NO | 用户自定义的订单号 | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

orderId 与 clientOrderId 必须至少发送一个

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": "4611875134427365377",        // 系统订单号
    "symbol": "BTCUSDT-200730-9000-C",  // 期权交易对
    "price": 100,                       // 委托价格
    "quantity": 1,                      // 委托数量
    "executedQty": 0,                   // 成交数量
    "fee": 0,                           // 手续费
    "side": "BUY",                      // 买卖方向
    "type": "LIMIT",                    // 订单类型
    "timeInForce": "GTC",               // 有效方法
    "createDate": 1592465880683,        // 委托时间
    "status": "ACCEPTED",               // 订单状态
    "avgPrice": 0,                      // 成交均价
    "source": "WEB",                    // 订单来源
    "clientOrderId": ""                 // 客户端订单ID
  }
}
```

## 期权查询当前挂单 (TRADE)

`GET /api/v1/openOrders  (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
orderId | LONG | NO | 返回该orderId及之后的订单，缺省返回最近的订单 | 100000
startTime | LONG | NO | 起始时间 | 1593511200000
endTime | LONG | NO | 结束时间 | 1593512200000
limit | INT | NO | 返回的结果集数量 默认值:100 最大值:1000 | 100
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
      {
        "id": "4611875134427365377",        // 系统订单号
        "symbol": "BTCUSDT-200730-9000-C",  // 期权交易对
        "price": 100,                       // 委托价格
        "quantity": 1,                      // 委托数量
        "executedQty": 0,                   // 成交数量
        "fee": 0,                           // 手续费
        "side": "BUY",                      // 买卖方向
        "type": "LIMIT",                    // 订单类型
        "timeInForce": "GTC",               // 有效方法
        "createDate": 1592465880683,        // 委托时间
        "status": "ACCEPTED",               // 订单状态
        "avgPrice": 0,                      // 成交均价
        "source": "WEB",                    // 订单来源
        "clientOrderId": ""                 // 客户端订单ID
      }
  ]
}
```

## 期权查询历史委托 (TRADE)

`GET /api/v1/historyOrders  (HMAC SHA256)`

**权重:**
1

**参数:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
orderId | LONG | NO | 返回该orderId及之后的订单，缺省返回最近的订单 | 100000
startTime | LONG | NO | 起始时间 | 1593511200000
endTime | LONG | NO | 结束时间 | 1593512200000
limit | INT | NO | 返回的结果集数量 默认值:100 最大值:1000 | 100
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**响应:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
      {
        "id": "4611875134427365377",        // 系统订单号
        "symbol": "BTCUSDT-200730-9000-C",  // 期权交易对
        "price": 100,                       // 委托价格
        "quantity": 1,                      // 委托数量
        "executedQty": 0,                   // 成交数量
        "fee": 0,                           // 手续费
        "side": "BUY",                      // 买卖方向
        "type": "LIMIT",                    // 订单类型
        "timeInForce": "GTC",               // 有效方法
        "createDate": 1592465880683,        // 委托时间
        "status": "CANCELLED",              // 订单状态
        "avgPrice": 0,                      // 成交均价
        "source": "WEB",                    // 订单来源
        "clientOrderId": ""                 // 客户端订单ID
      }
  ]
}
```
