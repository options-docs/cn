# 基本信息

## Rest 基本信息

* 本篇列出REST接口的baseurl **https://testnet.binanceops.com**
* 所有接口的响应都是JSON格式
* 响应中如有数组，数组元素以时间升序排列，越早的数据越提前。
* 所有时间、时间戳均为UNIX时间，单位为毫秒

## 接口的基本信息

* GET方法的接口, 参数必须在query string中发送.
* POST, PUT, 和 DELETE 方法的接口, 参数可以在 query string中发送，也可以在 request body中发送(content type application/x-www-form-urlencoded)。允许混合这两种方式发送参数。
* 对参数的顺序不做要求。
* 但如果同一个参数名在query string和request body中都有，query string中的会被优先采用。

## 接口鉴权类型

* 每个接口都有自己的鉴权类型，鉴权类型决定了访问时应当进行何种鉴权
* 如果需要 API-key，应当在HTTP头中以X-MBX-APIKEY字段传递
* API-key 与 API-secret 是大小写敏感的
* 可以在网页用户中心修改API-key 所具有的权限，例如读取账户信息、发送交易指令、发送提现指令

鉴权类型 | 描述 
------------ | ------------ 
NONE | 不需要鉴权的接口
TRADE | 需要有效的API-KEY和签名 
USER_DATA | 需要有效的API-KEY和签名 
USER_STREAM | 需要有效的API-KEY 
MARKET_DATA | 需要有效的API-KEY 

## 需要签名的接口

* 调用这些接口时，除了接口本身所需的参数外，还需要传递signature即签名参数。
* 签名使用HMAC SHA256算法. API-KEY所对应的API-Secret作为 HMAC SHA256 的密钥，其他所有参数作为HMAC SHA256的操作对象，得到的输出即为签名。
* 签名大小写不敏感。
* 当同时使用query string和request body时，HMAC SHA256的输入query string在前，request body在后

## 时间同步安全

* 签名接口均需要传递timestamp参数, 其值应当是请求发送时刻的unix时间戳(毫秒)
* 服务器收到请求时会判断请求中的时间戳，如果是5000毫秒之前发出的，则请求会被认为无效。这个时间窗口值可以通过发送可选参数recvWindow来自定义。
* 另外，如果服务器计算得出客户端时间戳在服务器时间的‘未来’一秒以上，也会拒绝请求。

**关于交易时效性** 互联网状况并不100%可靠，不可完全依赖,因此你的程序本地到币安服务器的时延会有抖动. 这是我们设置recvWindow的目的所在，如果你从事高频交易，对交易时效性有较高的要求，可以灵活设置recvWindow以达到你的要求。
>不推荐使用5秒以上的recvWindow


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

名称 | 类型 | 是否必需 | 描述 |  示例
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

名称 | 类型 | 是否必需 | 描述 | 示例
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

名称 | 类型 | 是否必需 | 描述 | 示例
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

名称 | 类型 | 是否必需 | 描述 | 示例
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

名称 | 类型 | 是否必需 | 描述 | 示例
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

名称 | 类型 | 是否必需 | 描述 | 示例
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

名称 | 类型 | 是否必需 | 描述 | 示例
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

名称 | 类型 | 是否必需 | 描述 |  示例
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

名称 | 类型 | 是否必需 | 描述 |  示例
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

名称 | 类型 | 是否必需 | 描述 |  示例
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

名称 | 类型 | 是否必需 | 描述 |  示例
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

名称 | 类型 | 是否必需 | 描述 |  示例
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

名称 | 类型 | 是否必需 | 描述 | 示例
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

名称 | 类型 | 是否必需 | 描述 |  示例
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

名称 | 类型 | 是否必需 | 描述 |  示例
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | 期权交易对 | BTCUSDT-200730-9000-C
side | ENUM | YES | 买卖方向 SELL, BUY | BUY
type | ENUM | YES | 订单类型 LIMIT, MARKET | LIMIT
quantity | DECIMAL | YES | 下单数量 | 3
price | DECIMAL | NO | 委托价格 | 1000
timeInForce | ENUM | NO | 有效方法（默认GTC） | GTC
reduceOnly | BOOLEAN | NO | 是否仅平仓单（默认false） | false
newOrderRespType | ENUM | NO | "ACK", "RESULT", 默认 "ACK" | ACK
clientOrderId | STRING | NO | 用户自定义的订单号，不可以重复出现在挂单中 | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

根据 order type的不同，某些参数强制要求，具体如下:

类型 | 强制要求的参数
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
    "reduceOnly": false,                // 是否仅平仓单
    "clientOrderId": ""                 // 客户端订单ID
  }
}
```

## 期权撤销订单 (TRADE)

`DELETE /api/v1/order  (HMAC SHA256)`

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述 |  示例
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
    "reduceOnly": false,                // 是否仅平仓单
    "clientOrderId": ""                 // 客户端订单ID
  }
}
```

## 期权撤销全部订单 (TRADE)

`DELETE /api/v1/allOpenOrders (HMAC SHA256)`

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述 |  示例
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

名称 | 类型 | 是否必需 | 描述 |  示例
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
    "reduceOnly": false,                // 是否仅平仓单
    "clientOrderId": ""                 // 客户端订单ID
  }
}
```

## 期权查询当前挂单 (TRADE)

`GET /api/v1/openOrders  (HMAC SHA256)`

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述 |  示例
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
        "reduceOnly": false,                // 是否仅平仓单
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

名称 | 类型 | 是否必需 | 描述 |  示例
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
        "reduceOnly": false,                // 是否仅平仓单
        "clientOrderId": ""                 // 客户端订单ID
      }
  ]
}
```



## Websocket账户信息推送

 本篇所列出API接口的base url : https://api.binance.com 

 用于订阅账户数据的 listenKey 从创建时刻起有效期为60分钟
 
 可以通过 PUT 一个 listenKey 延长60分钟有效期
 
 可以通过DELETE一个 listenKey 立即关闭当前数据流，并使该listenKey 无效
 
 在具有有效listenKey的帐户上执行POST将返回当前有效的listenKey并将其有效期延长60分钟
 
 websocket接口的baseurl: wss://stream.binance.com:9443
 
 U订阅账户数据流的stream名称为 /ws/<listenKey> 或 /stream?streams=<listenKey>
 
 每个链接有效期不超过24小时，请妥善处理断线重连。
 
 账户数据流的消息不保证严格时间序; 请使用 E 字段进行排序
 
 数据默认是GZIP压缩数据 连接成功后发送 `{"method":"BINARY", "params":["false"], "id":1}` 转换为文本数据

### 生成 Listen Key (USER_STREAM)
开始一个新的数据流。除非发送 keepalive，否则数据流于60分钟后关闭。如果该帐户具有有效的listenKey，则将返回该listenKey并将其有效期延长60分钟。

`GET /v1/private/user/userDataStream`

**权重:**
1

**参数:**
NONE

>**响应:**

```javascript
{
  "listenKey":"pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```
### 延长 Listen Key 有效期 (USER_STREAM)
有效期延长至本次调用后60分钟,建议每30分钟发送一个 put 请求。 

`PUT /v1/private/user/userDataStream`

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述 |  示例
------------ | ------------ | ------------ | ------------ | ------------ 
listenKey | STRING | YES | listenKey | "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"

>**响应:**

```javascript
{
  "listenKey":"pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```
### 删除 Listen Key (USER_STREAM)
开始一个新的数据流。除非发送 keepalive，否则数据流于60分钟后关闭。如果该帐户具有有效的listenKey，则将返回该listenKey并将其有效期延长60分钟。

`DELETE /v1/private/user/userDataStream`

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述 |  示例
------------ | ------------ | ------------ | ------------ | ------------ 
listenKey | STRING | YES | listenKey | "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"


>**响应:**

```javascript
{}
```
### Payload: 账户数据
当下列情形发生时更新:
账户发生充值或提取

交易账户之间发生划转(例如 现货向杠杆账户划转)

持仓变化

>**响应:**

```javascript
{
    "e":"ACCOUNT_UPDATE",                   // 事件类型
    "E":1591696384141,                      // 事件时间
    "B":[
        {
          
            "b":"100007992.26053177",       // 账户余额
            "m":"0",                        // 持仓价值
            "u":"458.782655111111",         // 未实现盈亏
            "U":"458.782655111111",         // 未实现盈亏 (卖方)
            "o":"-13238.246342",            // 委托保证金额
            "p":"-18852.328456",            // 持仓保证金额
            "r":"-15452.328456",            // 减仓保证金额
            "M":"-15452.328456"             // 维持保证金额
        }
    ],
    //持仓信息变化 如果有变化包含P属性 没有变化则不包含
    "P":[
          {
           "S":symbol,                      // 合约类型
           "c":1,                           // 当前持仓量
           "r":1,                           // 可平仓量
           "p":1,                           // 持仓价值
           "a":1                            // 持仓均价
          }
        ]
}
```
### Payload: 订单数据
当下列情形发生时更新:
订单变化 下单 撤单 成交



>**响应:**

```javascript
 "e":"ORDER_TRADE_UPDATE",                  // 事件类型
    "E":1591698525864,                      // 事件时间
    "o":[
        {
            "T":0,                          // 交易时间 
            "oid":1,                        // 订单Id
            "S":symbol,                     // 合约名称
            "cid":"zdy",                    // 用户自定义信息
            "q":"0.111",                    // 交易量
            "stp":0,                        // selfTradePrevention
            "r":false,                      // 减仓
            "c":false,                      // 暂未使用
            "s":0,                          // 状态
            "e":"1",                        // 成交量
            "ec":"1",                       // 成交金额
            "f":"0.011"                     // 手续费
        }
    ]
}
```
### 公共数据 Payload: 24小时TICKER
#### 订阅一个信息流
请求
```javascript
{
"method": "SUBSCRIBE",
"params":
[
"BTCUSDT-200630-9000-P@ticker"
],
"id": 1
}
```
24ticker
>**响应:**

```javascript
{
    "e":"ticker",                           // 事件类型
    "E":1591677962357,                      // 事件事件
    "s":"BTCUSDT-200630-9000-P",            // 币种
    "o":"1000",                             // 24小时开盘价
    "h":"1000",                             // 最高价
    "l":"1000",                             // 最低价
    "c":"1000",                             // 最新价
    "V":"2",                                // 交易量
    "A":"0",                                // 交易金额
    "p":"0",                                // 涨跌幅
    "Q":"2000",                             // 最后成交量
    "F":1,                                  // 第一笔交易id
    "L":1,                                  // 最后一笔交易Id
    "n":1,                                  // 交易笔数
    "b":"0",                                // 买隐含波动率
    "a":"0",                                // 卖隐含波动率
    "d":"0",                                // delta
    "t":"0",                                // theta
    "g":"0",                                // gamma
    "v":"0"                                 // vega
}
```
### 公共数据 Payload: 最近成交

请求
```javascript
{
"method": "SUBSCRIBE",
"params":
[
"BTCUSDT-200630-9000-P@trade"
],
"id": 1
}
```

>**响应:**

```javascript
{
    "e":"trade",                        // 事件类型
    "E":1591677941092,                  // 事件时间
    "s":"BTCUSDT-200630-9000-P",        // 币种
    "t":[                               // 历史交易
        {
            "t":1,                      // 交易Id
            "p":"1000",                 // 交易价格
            "q":"-2",                   // 交易量
            "b":4611781675939004417,    // 卖单Id
            "a":4611781675939004418,    // 卖单Id
            "T":1591677567872,          // 成交时间
            "s":"-1"                    // 方向
        }
    ]
}
```

### 公共数据 Payload: k线

请求
```javascript
{
"method": "SUBSCRIBE",
"params":
[
"BTCUSDT-200630-9000-P@kline_1m"
],
"id": 1
}
```
```
周期
```html
"1m", 
"3m", 
"5m", 
"15m"
"30m"
"1h",
"2h",
"4h",
"6", 
"12h
"1d", 
"3d", 
"1w",
```
>**响应:**

```javascript
{
    "e":"kline",                        // 事件类型
    "E":1591677941085,                  // 事件事件
    "s":"BTCUSDT-200630-9000-P",        // 币种
    "k":[{                              // 倒数第二条
        "t":1591677900000,              // k线事件
        "i":"1m",                       // k线周期
        "F":0,                          // 第一个交易id
        "L":0,                          // 最后一个交易id
        "o":"1000",                     // 开
        "h":"1000",                     // 高
        "l":"1000",                     // 低
        "c":"1000",                     // 收
        "v":"0",                        // 量
        "n":0,                          // 交易笔数
        "q":"0",                        // 成交金额
        "x":false,                      // 当前k线是否完成
        "V":"0",                        // taker成交量
        "Q":"0"                         // taker成交金额
    },{                                 // 最新k线数据
        "t":1591677900000,              // k线事件
        "i":"1m",                       // k线周期
        "F":0,                          // 第一个交易id
        "L":0,                          // 最后一个交易id
        "o":"1000",                     // 开
        "h":"1000",                     // 高
        "l":"1000",                     // 低
        "c":"1000",                     // 收
        "v":"0",                        // 量
        "n":0,                          // 交易笔数
        "q":"0",                        // 成交金额
        "x":false,                      // 当前k线是否完成
        "V":"0",                        // taker成交量
        "Q":"0"                         // taker成交金额
    }
  ]
}
```
### 公共数据 Payload: 深度
请求
```javascript
{
"method": "SUBSCRIBE",
"params":
[
"BTCUSDT-200630-9000-P@depth5"
],
"id": 1
}

```
周期
```html
"5", 
"10", 
"20", 
"50",
"100"
"1000"
```
>**响应:**

```javascript
{
    "e":"depth",                        // 事件类型
    "E":1591695934033,                  // 事件时间
    "s":"BTCUSDT-200630-9000-P",        // 币种
    "b":[                               // 买单
        [
            "200",                      // 价格
            "3"                         // 量
        ],
        [
            "101",
            "1"
        ],
        [
            "100",
            "2"
        ]
    ],
    "a":[                               // 卖单
        [
            "1000",
            "89"
        ]
    ]
}
```


