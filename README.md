## QB API文档

#### [API调用说明](#api调用说明)
- [调用方式](#调用方式)
- [签名规则](#签名规则)
- [公共参数](#公共参数)
- [返回数据结构](#返回数据结构)

#### [API列表](#api列表)
##### [行情](#行情)
- [全部交易对最新成交行情](#全部交易对最新成交行情)
- [获得指定交易对最新成交记录](#获得指定交易对最新成交记录)
- [获得指定交易对市场深度](#获得指定交易对市场深度)
- [单个交易对24小时行情概览](#单个交易对24小时行情概览)

##### [交易](#交易)
- [下单](#下单)
- [撤单](#撤单)
- [撤销指定交易对所有买单](#撤销指定交易对所有买单)
- [撤销指定交易对所有卖单](#撤销指定交易对所有卖单)
- [订单详情](#订单详情)
- [订单成交明细](#订单成交明细)
- [当前委托订单](#获取当前委托订单)
- [获取历史成交信息](#获取历史成交信息)

##### [账户](#账户)
- [账户余额](#账户余额)

##### [其它接口](#其它接口)
- [获得交易对信息](#获得交易对信息)

#### [错误码](#错误码)

## API调用说明
### API地址

https://api2.qb.com 
https://api1.qbtrade.net (备用地址)

### 调用方式
使用标准的HTTP协议参数格式，URLEncode字符编码为UTF-8；注：URLEncode为http参数传输的标准，大部分的httpclient已经自动处理。 为了数据安全，平台加入签名认证机制。 用户首先需要在QB平台申请accessKey及对应的secretKey。客户端要保证secretKey不被泄露。

### 签名规则
调用API时使用签名，sign=XXXX。
生成签名的步骤及规则：

> - 1) 将请求中所有参数（`值为空字符串除外`）以参数名按字典序排序；
> - 2) 将排序好的参数键值对用"&"拼接（`value用urlencode编码`）,例如：k1=v1&k2=v2&k3=v3；
> - 3) 用Hmac SHA256算法对步骤2）中生成的进行hash h=hmacsha256("secret",'k1=v1&k2=v2&k3=v3')
> - 4) 对上述hash值进行base64编码,并转小写 sign=lower(base64(h))

### 公共参数
| 参数名    | 类型   | 说明                         | 是否必须 | 默认值 | 备注                                             |
| --------- | ------ | ---------------------------- | -------- | ------ | ------------------------------------------------ |
| accessKey | string | 平台分配的accessKey          | Y        |        |                                                  |
| signV     | string | 签名版本，固定为1            | Y        | 1      |                                                  |
| ts        | string | 客户端当前时间戳（精确到秒） | Y        |        | 客户端必须校对本地时间，服务器时间差不能大于30秒 |
| sign      | string | 签名                             |   Y       |        |                                                  |
### 返回数据结构
- 成功时
```
{
  "status":"ok",
  "result":{} //成功时返回的业务数据，下列api中返回数据是指这一项
}
```
- 失败时
```
{
  "status":"error",
  "errorCode":"100000"  //失败时错误码，详细说明见错误码列表
}
```
> 下列api文档中的返回数据只展示了这里的result项



## API列表

### 行情

###### 全部交易对最新成交行情
- `GET /api/v1/market/tickers`
- 参数： 无
- 返回：

```
{
  "data":[
       {  
            "open":0.044297,      //  开盘价
            "close":0.042178,     // 收盘价
            "low":0.040110,       //  最低价
            "high":0.045255,      //  最高价
            "symbol":"btc_usdt",     // 交易对(coinName_marketName)
            "amount":2286,  //24小时交易量
            "volume":8553680.092248  //24小时成交额
        },
       ......
  ]
}
```

###### 获得指定交易对最新成交记录
- `GET /api/v1/market/trade`
- 参数：

|参数名|类型|说明|是否必须|默认值|备注|
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|symbol|string|交易对|Y|||

- 返回：
按成交时间降序排列
```
{
  "data":[
     {
        "id": 成交id,
        "price": 成交价钱,
        "amount": 成交量,
        "type": "buy|sell",
        "ts": 成交时间戳
      },
      ...
  ]
}
```

###### 获得指定交易对市场深度
- `GET /api/v1/market/depth`
- 参数：

|参数名|类型|说明|是否必须|默认值|备注|
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|symbol|string|交易对|Y|||
|type|int|合并类型0表示不合并，目前仅支持0|Y|0||

- 返回：

```
{
    "ts" : 1422222222, //当前时间戳
    "bids": [ // 买入
      [7964, 0.0678], // [price, amount]
      [7963, 0.9162],
      [7961, 0.1],
      [7960, 12.8898],
      [7958, 1.2],
      ...
    ],
    "asks": [ //卖出
      [7979, 0.0736],
      [7980, 1.0292],
      [7981, 5.5652],
      [7986, 0.2416],
      [7990, 1.9970],
      [7995, 0.88],
      ...
    ]
  }
```

###### 单个交易对24小时行情概览

- `GET /api/v1/market/summary`
- 参数：

|参数名|类型|说明|是否必须|默认值|备注|
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|symbol|string|交易对|Y|||

- 返回：

```
  {
    "amount": 4316.4346,//24小时成交量,
    "high": 8119.00,//近24小时最高价,
    "ts": 1489464451,//统计时间时间戳
    "low": 7875.00,//近24小时最低价,
    "vol": 34497276.905760//
  }
```

### 交易
###### 下单
- `POST /api/v1/order/place`
- 参数：

|参数名|类型|说明|是否必须|默认值|备注|
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|symbol|string|交易对|Y|||
|price|float|价格|Y|0.0|市价单不用传参|
|amount|float|数量|Y|0.0|大于最小下单量限制|
|type|string|下单类型|Y||market-市价(无效)，limit-限价，best_price-最优价委托|
|side|string|买卖类型|Y||sell-卖，buy-买|


- 返回：

```
  {
    "orderId":"1234567"
  }
```

###### 撤单
- `POST /api/v1/order/cancel`
- 参数：

|参数名|类型|说明|是否必须|默认值|备注|
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|symbol|string|交易对|Y|||
|orderId|string|订单id|Y|||


- 返回：

```
  {
    "orderId":"1234567",
    "result":1 // 撤销状态，1-撤单申请已提交
  }
```
###### 撤销指定交易对所有买单
- `POST /api/v1/order/cancelallbuy`
- 参数：

|参数名|类型|说明|是否必须|默认值|备注|
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|symbol|string|交易对|Y|||

- 返回：

```
  {
    "result":1 // 撤销状态，1-撤销成功
  }
```
###### 撤销指定交易对所有卖单
- `POST /api/v1/order/cancelallsell`
- 参数：

|参数名|类型|说明|是否必须|默认值|备注|
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|symbol|string|交易对|Y|||

- 返回：

```
  {
    "result":1 // 撤销状态，1-撤销成功
  }
```

###### 订单详情
- `GET /api/v1/order/detail`
- 参数：

|参数名|类型|说明|是否必须|默认值|备注|
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|symbol|string|交易对|Y|||
|orderId|string|订单id|Y|||

- 返回：

```
   {
    "id": "59378",
    "symbol": "eth_usdt",
    "amount": 10.1000000000,
    "price": 100.1000000000,
    "createdAt": 1494901162595, //下单时间
    "type": "market|limit|best_price",
    "side": "buy|sell",
    "dealedAmount": 10.1000000000, //成交量
    "dealedAvgPrice":1.25555, //平均成交价
    "fee": 0.0202000000, // 交易费
    "finishedAt": 1494901400468, //成交时间
    "state": 0 // 当前状态 0:未成交 1:部分成交 2:全部成交 3:已撤单 4:撤单中 5:市价单已结束 6:市价单部分成交 7:撤单部分成交',
  }
```
###### 订单成交明细
- `GET /api/v1/order/matchdetail`
- 参数：

|参数名|类型|说明|是否必须|默认值|备注|
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|symbol|string|交易对|Y|||
|orderId|string|订单id|Y|||

- 返回：

```
{
  "data":[
    {
      "id": 29553, // 交易id
      "symbol": "eth_usdt",
      "side":"sell|buy",
      "price": 100.1000000000,
      "amount": 9.1155000000,
      "fee": 0.0182310000,
      "dealAt": 1494901400 //成交时间
    },
   ...
  ]
}
```
###### 获取当前委托订单
- `GET /api/v1/order/openOrders`
- 参数：

| 参数名 |  类型  |          说明           | 是否必须 | 默认值 | 备注 |
|:------:|:------:|:-----------------------:|:--------:|:------:|:----:|
| symbol | string |         交易对          |    Y     |        |      |
|  side  | string | 卖单或买单:buy,sell,all |    F     |  all   |      |
| size  |  int   |   返回的数量，最大支持500   |    F     |   30   |      |


- 返回：

```
{
  "data":[
   {
    "id": "59378",
    "symbol": "eth_usdt",
    "amount": 10.1000000000,
    "price": 100.1000000000,
    "createdAt": 1494901162595, //下单时间
    "type": "market|limit|best_price",
    "side": "buy|sell",
    "dealedAmount": 10.1000000000, //成交量
    "dealedAvgPrice":1.25555, //平均成交价
    "fee": 0.0202000000, // 交易费
    "finishedAt": 1494901400468, //成交时间
    "state": 0 // 当前状态 0:未成交 1:部分成交 2:全部成交 3:已撤单 4:撤单中 5:市价单已结束 6:市价单部分成交 7:撤单部分成交',
   },
   ...
  ]
}
```
###### 获取历史成交信息
- `GET /api/v1/order/historyTrades`
- 参数：

|  参数名   |  类型  |                         说明                         | 是否必须 | 默认值 | 备注 |
|:---------:|:------:|:----------------------------------------------------:|:--------:|:------:|:----:|
|  symbol   | string |                        交易对                        |    Y     |        |      |
|   side    | string |               卖单或买单:buy,sell,all                |    F     |  all   |      |
| startTime | string |                      成交开始时间戳                      |          |        |      |
|  endTime  | string |                      成交结束时间戳                      |          |        |      |
- 说明： 最大支持时间范围为60天

- 返回：

```
{
  "data":[
    {
      "id": 29553, // 交易id
      "orderId":354678
      "symbol": "eth_usdt",
      "side":"sell|buy",
      "price": 100.1000000000,
      "amount": 9.1155000000,
      "fee": 0.0182310000,
      "dealAt": 1494901400 //成交时间
    },
   ...
  ]
}
```

### 账户
###### 资产情况
- `GET /api/v1/account/assets`
- 参数： 无
- 返回：

```
{
   "data": [
      {
        "currency": "usdt",
        "frozen": 234.0047, //冻结数量
        "avail": 328048.119992 // 可用数量
      },
      ....
   ]
}
```
### 其它接口
###### 获得交易对信息
- `GET /api/v1/common/symbols`
- 参数：无

- 返回：

```
{
  "data": [
    {
        "symbol":"btc_usdt"
        "coin": "btc",
        "market": "usdt",
        "pricePrecision": 2, //价格精度
        "amountPrecision": 4, // 数量精度
        "partition": "main|innovation" //交易对所在分区，主区|创新区
    },
    ...
  ]
}
```

## 错误码
| 错误码 | 错误说明                      |     |
| ------ | ----------------------------- | --- |
| -1     | 未知错误                      |     |
| 1000   | 错误请求                      |     |
| 1001   | 参数错误                      |     |
| 1002   | 签名错误                      |     |
| 1003   | 系统忙                        |     |
| 1004   | 客户端时间错误                |     |
|        |                               |     |
| 2000   | 订单不存在                    |     |
| 2001   | 交易对不存在                  |     |
| 2002   | 余额不足                      |     |
| 2003   | 下单价格超限                  |     |
| 2004   | 下单数量超限                  |     |
| 2005   | 价格精度超限                  |     |
| 2006   | 数量精度超限                  |     |
| 2007   | 订单side无效，只允许buy和sell |     |
| 2008   | 订单状态无效                  |     |
| 2009   | 订单类型无效                  |     |
| 2010   | 拒绝下单                      |     |
| 2011   | 拒绝撤单                      |     |
| 2012   | 限价单价格过低                  |     |
| 2013   | 限价单价格过高                  |     |
| 2014   | 最优价委托不可用                |     |
|        |                               |     |
| 3001   | 用户未登录                    |     |
| 3002   | 用户无权限                    |     |
| 3003   | 资金密码错误                  |     |
