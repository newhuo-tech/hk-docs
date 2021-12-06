---
title: 火币香港 API 文档

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href='HTTPS://WWW.HUOBI.COM.HK/zh-hk/user/api/'>创建 API Key </a>
includes:

search: true
---



# 简介

欢迎使用火币香港 API！  

此文档是火币香港API的唯一官方文档，火币香港API提供的功能和服务会在此文档持续更新，并会发布公告进行通知，建议您关注和订阅我们的公告，及时获取相关信息。


**如何阅读本文档**


文档下方是某个业务的API文档，其中左侧是目录，中间是正文，右侧是请求参数和响应结果的示例。

以下是现货API文档各章节主要内容

第一部分是概要介绍：

- **快速入门**：该章节对火币香港API做了简单且全方位的介绍，适合第一次使用火币API的用户。
- **常见问题**：该章节列举了使用火币香港API时常见的、和具体API无关的通用问题。


第二部分是每个接口类的详细介绍，每个接口类一个章节，每个章节分为如下内容：

- **简介**：对该接口类进行简单介绍，包括一些注意事项和说明。
- ***具体接口***：介绍每个接口的用途、限频、请求、参数、返回等详细信息。
- **常见错误码**：介绍该接口类下常见的错误码及其说明。
- **常见问题**：介绍该接口类下常见问题和解答。

# 快速入门

## 接入准备

如需使用API ，请先登录网页端，完成API key的申请和权限配置，再据此文档详情进行开发和交易。  

您可以点击 <a href='HTTPS://WWW.HUOBI.COM.HK/zh-hk/user/api/'>这里 </a> 创建 API Key。

每个母用户可创建20组Api Key，每个Api Key可对应设置读取、交易两种权限。  

权限说明如下：

- 读取权限：读取权限用于对数据的查询接口，例如：订单查询、成交查询等。
- 交易权限：交易权限用于下单、撤单、划转类接口。


创建成功后请务必记住以下信息：

- `Access Key`  API 访问密钥
  
- `Secret Key`  签名认证加密所使用的密钥（仅申请时可见）

<aside class="notice">
每个 API Key 最多可绑定 20个IP 地址(主机地址或网络地址)。
</aside>
<aside class="warning">
<red><b>风险提示</b></red>：这两个密钥与账号安全紧密相关，无论何时都请勿将二者<b>同时</b>向其它人透露。API Key的泄露可能会造成您的资产损失（即使未开通提币权限），若发现API Key泄露请尽快删除该API Key。
</aside> 



## 接口类型

火币香港为用户提供两种接口，您可根据自己的使用场景和偏好来选择适合的方式进行查询行情、交易或提币。  

### REST API

REST，即Representational State Transfer的缩写，是目前较为流行的基于HTTP的一种通信机制，每一个URL代表一种资源。

交易或资产提币等一次性操作，建议开发者使用REST API进行操作。

### WebSocket API

WebSocket是HTML5一种新的协议（Protocol）。它实现了客户端与服务器全双工通信，通过一次简单的握手就可以建立客户端和服务器连接，服务器可以根据业务规则主动推送信息给客户端。

市场行情和买卖深度等信息，建议开发者使用WebSocket API进行获取。

**接口鉴权**

以上两种接口均包含公共接口和私有接口两种类型。

公共接口可用于获取基础信息和行情数据。公共接口无需认证即可调用。

私有接口可用于交易管理和账户管理。每个私有请求必须使用您的API Key进行签名验证。

## 接入URLs
  

**REST API**

**`https://api.huobi.com.hk`**  


**Websocket Feed（行情，不包含MBP增量行情）**

**`wss://api.huobi.com.hk/ws`**  



**Websocket Feed（行情，仅MBP增量行情）**

**`wss://api.huobi.com.hk/feed`**  



**Websocket Feed（资产和订单）**

**`wss://api.huobi.com.hk/ws/v2`**  




</aside>
<aside class="notice">
鉴于延迟高和稳定性差等原因，不建议通过代理的方式访问火币香港API。
</aside>
<aside class="notice">
为保证API服务的稳定性，建议使用香港AWS云服务器进行访问。
</aside> 

## 签名认证

### 签名说明

API 请求在通过 internet 传输的过程中极有可能被篡改，为了确保请求未被更改，除公共接口（基础信息，行情数据）外的私有接口均必须使用您的 API Key 做签名认证，以校验参数或参数值在传输途中是否发生了更改。  
每一个API Key需要有适当的权限才能访问相应的接口，每个新创建的API Key都需要分配权限。在使用接口前，请查看每个接口的权限类型，并确认你的API Key有相应的权限。

一个合法的请求由以下几部分组成：

- 方法请求地址：即访问服务器地址 
- API 访问Id（AccessKeyId）：您申请的 API Key 中的 Access Key。
- 签名方法（SignatureMethod）：用户计算签名的基于哈希的协议，此处使用 HmacSHA256。
- 签名版本（SignatureVersion）：签名协议的版本，此处使用2。
- 时间戳（Timestamp）：您发出请求的时间 (UTC 时间)  。如：2017-05-11T16:22:06。在查询请求中包含此值有助于防止第三方截取您的请求。
- 必选和可选参数：每个方法都有一组用于定义 API 调用的必需参数和可选参数。可以在每个方法的说明中查看这些参数及其含义。 
  - 对于 GET 请求，每个方法自带的参数都需要进行签名运算。
  - 对于 POST 请求，每个方法自带的参数不进行签名认证，并且需要放在 body 中。
- 签名：签名计算得出的值，用于确保签名有效和未被篡改。

### 签名步骤

规范要计算签名的请求 因为使用 HMAC 进行签名计算时，使用不同内容计算得到的结果会完全不同。所以在进行签名计算前，请先对请求进行规范化处理。下面以查询某订单详情请求为例进行说明：

查询某订单详情时完整的请求URL

`https://api.huobi.com.hk/v1/order/orders?`

`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx`

`&SignatureMethod=HmacSHA256`

`&SignatureVersion=2`

`&Timestamp=2017-05-11T15:19:30`

`&order-id=1234567890`

**1. 请求方法（GET 或 POST，WebSocket用GET），后面添加换行符 “\n”**

例如：
`GET\n`

**2. 添加小写的访问域名，后面添加换行符 “\n”**

例如：
`
api.huobi.com.hk\n
`

**3. 访问方法的路径，后面添加换行符 “\n”**

例如查询订单：<br>
`
/v1/order/orders\n
`

例如WebSocket v2<br>
`
/ws/v2
`

**4. 对参数进行URL编码，并且按照ASCII码顺序进行排序**

例如，下面是请求参数的原始顺序，且进行URL编码后


`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx`

`order-id=1234567890`

`SignatureMethod=HmacSHA256`

`SignatureVersion=2`

`Timestamp=2017-05-11T15%3A19%3A30`

<aside class="notice">
使用 UTF-8 编码，且进行了 URL 编码，十六进制字符必须大写，如 “:” 会被编码为 “%3A” ，空格被编码为 “%20”。
</aside>
<aside class="notice">
时间戳（Timestamp）需要以YYYY-MM-DDThh:mm:ss格式添加并且进行 URL 编码。时间戳有效时间5分钟。
</aside>

经过排序之后

`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx`

`SignatureMethod=HmacSHA256`

`SignatureVersion=2`

`Timestamp=2017-05-11T15%3A19%3A30`

`order-id=1234567890`

**5. 按照以上顺序，将各参数使用字符 “&” 连接**


`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=2017-05-11T15%3A19%3A30&order-id=1234567890`

**6. 组成最终的要进行签名计算的字符串如下**

`GET\n`

`api.huobi.com.hk\n`

`/v1/order/orders\n`

`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=2017-05-11T15%3A19%3A30&order-id=1234567890`


**7. 用上一步里生成的 “请求字符串” 和你的密钥 (Secret Key) 生成一个数字签名**


1. 将上一步得到的请求字符串和 API 私钥作为两个参数，调用HmacSHA256哈希函数来获得哈希值。
2. 将此哈希值用base-64编码，得到的值作为此次接口调用的数字签名。

`4F65x5A2bLyMWVQj3Aqp+B4w+ivaA7n5Oi2SuYtCJ9o=`

**8. 将生成的数字签名加入到请求里**

对于Rest接口：

1. 把所有必须的认证参数添加到接口调用的路径参数里
2. 把数字签名在URL编码后加入到路径参数里，参数名为“Signature”。

最终，发送到服务器的 API 请求应该为

`https://api.huobi.com.hk/v1/order/orders?AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&order-id=1234567890&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=2017-05-11T15%3A19%3A30&Signature=4F65x5A2bLyMWVQj3Aqp%2BB4w%2BivaA7n5Oi2SuYtCJ9o%3D`

对于WebSocket接口：

1. 按照要求的JSON格式，填入参数和签名。
2. JSON请求中的参数不需要URL编码

例如：

`
{
    "action": "req", 
    "ch": "auth",
    "params": { 
        "authType":"api",
        "accessKey": "e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx",
        "signatureMethod": "HmacSHA256",
        "signatureVersion": "2.1",
        "timestamp": "2019-09-01T18:16:16",
        "signature": "4F65x5A2bLyMWVQj3Aqp+B4w+ivaA7n5Oi2SuYtCJ9o="
    }
}
`

## 子用户

子用户可以用来隔离资产与交易，资产可以在母子用户之间划转；子用户只能在子用户内进行交易，并且子用户之间资产不能直接划转，只有母用户有划转权限。  

子用户拥有独立的登录账号密码和 API Key，均由母用户在网页端进行管理。 

每个母用户可创建200个子用户，每个子用户可创建20组Api Key，每个Api Key可对应设置读取、交易两种权限。

子用户的 API Key 也需绑定 IP 地址。
  

子用户可以访问所有公共接口，包括基本信息和市场行情，子用户可以访问的私有接口如下：

| 接口                                                         | 说明                            |      |
| ------------------------------------------------------------ | ------------------------------- | ---- |
| [POST /v1/order/orders/place](#fd6ce2a756)                   | 创建并执行订单                  |      |
| [POST /v1/order/orders/{order-id}/submitcancel](#4e53c0fccd) | 撤销一个订单                    |      |
| [POST /v1/order/orders/submitCancelClientOrder](#client-order-id) | 撤销订单（基于client order ID） |      |
| [POST /v1/order/orders/batchcancel](#ad00632ed5)             | 批量撤销订单                    |      |
| [POST /v1/order/orders/batchCancelOpenOrders](#open-orders)  | 撤销当前委托订单                |      |
| [GET /v1/order/orders/{order-id}](#92d59b6aad)               | 查询一个订单详情                |      |
| [GET /v1/order/orders](#d72a5b49e7)                          | 查询当前委托、历史委托          |      |
| [GET /v1/order/openOrders](#95f2078356)                      | 查询当前委托订单                |      |
| [GET /v1/order/matchresults](#0fa6055598)                    | 查询成交                        |      |
| [GET /v1/order/orders/{order-id}/matchresults](#56c6c47284)  | 查询某个订单的成交明细          |      |
| [GET /v1/account/accounts](#bd9157656f)                      | 查询当前用户的所有账户          |      |
| [GET /v1/account/accounts/{account-id}/balance](#870c0ab88b) | 查询指定账户的余额              |      |

<aside class="notice">
其他接口子用户不可访问，如果尝试访问，系统会返回 “error-code 403”。
</aside>

## 业务字典

### 交易对

交易对由基础币种和报价币种组成。以交易对 BTC/USDT 为例，BTC 为基础币种，USDT 为报价币种。  

基础币种对应字段为 base-currency 。  

报价币种对应字段为 quote-currency 。 

### 账户

不同业务对应需要不同的账户，account-id为不同业务账户的唯一标识ID。  

account-id可通过/v1/account/accounts接口获取，并根据account-type区分具体账户。  






# 接入说明

## 接口概览

| 接口分类       | 分类链接                     | 概述                                             |
| -------------- | ---------------------------- | ------------------------------------------------ |
| 基础类         | /v1/common/*                 | 基础类接口，包括币种、币种对、时间戳等接口       |
| 行情类         | /market/*                    | 公共行情类接口，包括成交、深度、行情等           |
| 账户类         | /v1/account/*  /v1/subuser/* | 账户类接口，包括账户信息，子用户等               |
| 订单类         | /v1/order/*                  | 订单类接口，包括下单、撤单、订单查询、成交查询等 |

该分类为大类整理，部分接口未遵循此规则，请根据需求查看有关接口文档。



## 限频规则

除已单独标注限频值为NEW的接口外 -<br>
* 每个API Key 在1秒之内限制10次<br>
* 若接口不需要API Key，则每个IP在1秒内限制10次<br>

比如：

* 资产订单类接口调用根据API Key进行限频：1秒10次
* 行情类接口调用根据IP进行限频：1秒10次

## 请求格式

所有的API请求都是restful，目前只有两种方法：GET和POST

* GET请求：所有的参数都在路径参数里
* POST请求，所有参数以JSON格式发送在请求主体（body）里

## 返回格式

所有的接口都是JSON格式。其中v1和v2接口的JSON定义略有区别。

**v1接口返回格式**：最上层有四个字段：`status`, `ch`,  `ts` 和 `data`。前三个字段表示请求状态和属性，实际的业务数据在`data`字段里。

以下是一个返回格式的样例：

```json
{
  "status": "ok",
  "ch": "market.btcusdt.kline.1day",
  "ts": 1499223904680,
  "data": // per API response data in nested JSON object
}
```

| 参数名称 | 数据类型 | 描述                                                         |
| -------- | -------- | ------------------------------------------------------------ |
| status   | string   | API接口返回状态                                              |
| ch       | string   | 接口数据对应的数据流。部分接口没有对应数据流因此不返回此字段 |
| ts       | long     | 接口返回的UTC时间的时间戳，单位毫秒                          |
| data     | object   | 接口返回数据主体                                             |

**v2接口返回格式**：最上层有三个字段：`code`, `message` 和 `data`。前两个字段表示返回码和错误消息，实际的业务数据在`data`字段里。

以下是一个返回格式的样例：

```json
{
  "code": 200,
  "message": "",
  "data": // per API response data in nested JSON object
}
```

| 参数名称 | 数据类型 | 描述               |
| -------- | -------- | ------------------ |
| code     | int      | API接口返回码      |
| message  | string   | 错误消息（如果有） |
| data     | object   | 接口返回数据主体   |

## 数据类型

本文档对JSON格式中数据类型的描述做如下约定：

- `string`: 字符串类型，用双引号（"）引用
- `int`: 32位整数，主要涉及到状态码、大小、次数等
- `long`: 64位整数，主要涉及到Id和时间戳
- `float`: 浮点数，主要涉及到金额和价格，建议程序中使用高精度浮点型
- `object`: 对象，包含一个子对象{}
- `array`: 数组，包含多个对象

## 最佳实践

###安全类
- 强烈建议：在申请API Key时，请绑定您的IP地址，以此来保证您的API Key仅能在您自己的IP上使用。
- 强烈建议：不要将API Key暴露给任何人（包括第三方软件或机构），API Key代表了您的账户权限，API Key的暴露可能会对您的信息、资金造成损失，若API Key泄露，请尽快删除并重新创建。

###公共类
**API访问建议**


- 建议使用香港AWS云服务器进行访问。



###行情类
**行情类数据的获取**

- 行情类数据推荐使用WebSocket方式实时接收数据变化的推送，并在程序中进行数据的缓存，WebSocket方式实时性更高，且不受限频的影响。
- 建议用户不要在同一条WebSocket连接中订阅过多的主题和币种对，否则可能会因为上游数据量过大，导致网络阻塞，以至于连接断连。

**最新成交价格的获取**

- 推荐使用WebSocket的方式订阅`market.$symbol.trade.detail`主题，该主题为逐笔成交信息推送，该数据中成交的Price即为最新成交价格，且实时性更高。

**盘口深度的获取**

- 若对盘口数据的要求仅为买一卖一，可使用WebSocket订阅`market.$symbol.bbo`主题，该主题会在买一卖一变更时进行推送。
- 若需要多档数据，且对数据的实时性要求不高的情况下，可使用WebSocket订阅`market.$symbol.depth.$type`主题，该主题推送周期为1秒。
- 若需要多档数据，且对数据的实时性要求较高的情况下，可使用WebSocket订阅`market.$symbol.mbp.$levels`主题，并使用该主题发送req请求，构建本地深度，并增量跟新，该主题增量消息推送最快周期为100ms。
- 建议使用Rest（`/market/depth`）、WebSocket全量推送（`market.$symbol.depth.$type`）获取深度时，根据version字段对数据进行去重（舍弃较小的version）；使用WebSocket增量推送（`market.$symbol.mbp.$levels`）时，根据seqNum字段进行去重（舍弃较小的seqNum）。

**最新成交的获取**

- 建议订阅WebSocket成交明细（`market.$symbol.trade.detail`）主题时，可根据tradeId字段对数据进行去重。

###订单类
**下单接口（/v1/order/orders/place）**

- 建议用户下单前根据`/v1/common/symbols`接口中返回的币种对参数数据对下单价格、下单数量等参进行校验，避免产生无效的下单请求。
- 推荐下单时携带`client-order-id`参数，且建议用户保证该参数的唯一性（每次发送请求时都不同，且唯一），该参数能够防止在发起下单请求时由于网络或其他原因造成接口超时或没有返回的情况，在此情况下，可根据`client-order-id`对WebSocket中推送过来的数据进行验证，或使用`/v1/order/orders/getClientOrder`接口查询该订单信息。 （对于用户下单时传入的clientOrderId 的唯一性不进行校验，若发生多笔订单使用同一clientOrderId的情况，查询/撤单时将返回该clientOrderId对映的最近一笔订单。）

**搜索历史订单（/v1/order/orders）**

- 推荐使用`start-time`、`end-time`参数进行查询，该参数传入值为13位时间戳（精确至毫秒），使用该参数查询时最大查询窗口为48小时（2天），推荐按照小时进行查询，您搜索的时间范围越小，时间戳准确性越高，查询的效率会更高，可以根据上次查询的时间戳进行迭代查询。

**订单状态变化的通知**

- 建议使用WebSocket订阅`orders.$symbol.update`主题，该主题拥有更低的数据延迟以及更准确的消息顺序。
- 不建议使用WebSocket订阅`orders.$symbol`主题，该主题已由`orders.$symbol.update`取代，会在后续停止服务，请尽早更换使用。

###账户类
**资产变更**

- 使用WebSocket的方式，同时订阅`orders.$symbol.update`、`accounts.update#${mode}`主题，`orders.$symbol.update`用于接收订单的状态变化（创建、成交、撤销以及相关成交价格、数量信息），由于该主题在推送数据时，未经过清算，所以时效性更快，可根据`accounts.update#${mode}`主题接收相关资产的变更信息，以此来维护账户内的资金情况。
- 不建议WebSocket订阅`accounts`主题，该主题已由`accounts.update#${mode}`取代，会在后续停止服务，请尽早更换使用。



# 常见问题

本章列举了和具体API无关的通用常见问题，如网络、签名或通用错误等。

针对某类或某个API的问题，请查看每章API的错误码和常见问题。

### Q1：什么是UID和account-id？
A：UID是用户ID，是标示每个用户的唯一ID（包括母用户和子用户），UID可以在Web或App界面的个人信息里查看到，也可以通过接口 `GET /v2/user/uid`获得。

account-id则是该用户下不同业务账户的ID，需要通过`GET /v1/account/accounts`接口获取，并根据account-type区分具体账户。如果需要开通某个账户，需要首先通过Web或App开通并向该账户进行转账。

账户类型包括但不限于如下账户：

- spot 交易账户  
 
 

### Q2：一个用户可以申请多少个API Key？

每个母用户可创建20组API Key，每个API Key可对应设置读取、交易两种权限。 
每个母用户还可创建200个子用户，每个子用户可创建20组API Key，每个API Key可对应设置读取、交易两种权限。   

以下是三种权限的说明：  

- 读取权限：读取权限用于对数据的查询接口，例如：订单查询、成交查询等。  
- 交易权限：交易权限用于下单、撤单、划转类接口。  
 

### Q3：为什么经常出现断线、超时的情况？

请检查是否属于以下情况：

1. 建议使用香港AWS云服务器进行访问。 


### Q4：为什么WebSocket总是断开连接？

常见原因有：

1. 未回复Pong，WebSocket连接需在接收到服务端发送的Ping信息后回复Pong，保证连接的稳定。
2. 网络原因造成客户端发送了Pong消息，但服务端并未接收到。
3. 网络原因造成连接断开。
4. 建议用户做好WebSocket连接断连重连机制，在确保心跳（Ping/Pong）消息正确回复后若连接意外断开，程序能够自动进行重新连接。


### Q5：为什么签名认证总返回失败？

请检查如下可能的原因：

1、签名参数应该按照ASCII码排序。比如下面是原始的参数：

`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx`

`order-id=1234567890`

`SignatureMethod=HmacSHA256` 

`SignatureVersion=2`  

`Timestamp=2017-05-11T15%3A19%3A30` 

排序之后应该为：

`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx`

`SignatureMethod=HmacSHA256`

`SignatureVersion=2`

`Timestamp=2017-05-11T15%3A19%3A30`

`order-id=1234567890`

2、签名串需进行URL编码。比如：

- 冒号 `:`会被编码为`%3A`，空格会被编码为 `%20`
- 时间戳需要格式化为 `YYYY-MM-DDThh:mm:ss` ，经过URL编码之后为 `2017-05-11T15%3A19%3A30`  

3、签名需进行 base64 编码

4、Get请求参数需在签名串中

5、时间为UTC时间转换为YYYY-MM-DDTHH:mm:ss

6、检查本机时间与标准时间是否存在偏差（偏差应小于1分钟）

7、WebSocket发送验签认证消息时，消息体不需要URL编码

8、签名时所带Host应与请求接口时Host相同

如果您使用了代理，代理可能会改变请求Host，可以尝试去掉代理；

或者，您使用的网络连接库可能会把端口包含在Host内，可以尝试在签名用到的Host中包含端口，如“api.huobi.com.hk:443"



### Q6：调用接口返回Incorrect Access Key 错误是什么原因？

请检查URL请求中Access Key是否传递准确，例如：

1. Access Key没有传递
2. Access Key位数不准确
3. Access Key已经被删除
4. URL请求中参数拼接错误或者特殊字符没有进行编码导致服务端对AccessKey解析不正确

### Q7：调用接口返回 gateway-internal-error 错误是什么原因？

请检查是否属于以下情况：

1. 可能为网络原因或服务内部错误，请稍后进行重试。
2. 发送数据格式是否正确（需要标准JSON格式）。
3. POST请求头header需要声明为`Content-Type:application/json` 。

### Q8：调用接口返回 login-required 错误是什么原因？

请检查是否属于以下情况：

1. 未将AccessKey参数带入URL中。
2. 未将Signature参数带入URL中。

### Q9: 调用Rest接口返回HTTP 405错误 method-not-allowed 是什么原因？

该错误表明调用了不存在的Rest接口，请检查Rest接口路径是否准确。由于Nginx的设置，请求路径(Path)是大小写敏感的，请严格按照文档声明的大小写。



# 基础信息

## 简介

基础信息Rest接口提供了系统状态、市场状态、交易对信息、币种信息、币链信息、服务器时间戳等公共参考信息。



## 获取所有交易对

此接口返回所有火币香港站支持的交易对。

```shell
curl "https://api.huobi.com.hk/v1/common/symbols"
```


### HTTP 请求

- GET `/v1/common/symbols`

### 请求参数

此接口不接受任何参数。

> Responds:

```json
{
    "status": "ok",
    "data": [
        {
            "base-currency": "btc",
            "quote-currency": "usdt",
            "price-precision": 2,
            "amount-precision": 6,
            "symbol-partition": "main",
            "symbol": "btcusdt",
            "state": "online",
            "value-precision": 8,
            "min-order-amt": 0.0001,
            "max-order-amt": 1000,
            "min-order-value": 5,
            "limit-order-min-order-amt": 0.0001,
            "limit-order-max-order-amt": 1000,
            "sell-market-min-order-amt": 0.0001,
            "sell-market-max-order-amt": 100,
            "buy-market-max-order-value": 1000000,
            "leverage-ratio": 5,
            "super-margin-leverage-ratio": 3,
            "funding-leverage-ratio": 3,
            "api-trading": "enabled"
        },
    ......
    ]
}
```

### 返回字段

| 字段名称                   | 是否必须 | 数据类型 | 描述                                                         |
| -------------------------- | -------- | -------- | ------------------------------------------------------------ |
| base-currency              | true     | string   | 交易对中的基础币种                                           |
| quote-currency             | true     | string   | 交易对中的报价币种                                           |
| price-precision            | true     | integer  | 交易对报价的精度（小数点后位数），限价买入与限价卖出价格使用 |
| amount-precision           | true     | integer  | 交易对基础币种计数精度（小数点后位数），限价买入、限价卖出、市价卖出数量使用 |
| symbol-partition           | true     | string   | 交易区，可能值: [main，innovation]                           |
| symbol                     | true     | string   | 交易对                                                       |
| state                      | true     | string   | 交易对状态；可能值: [online，offline,suspend] online - 已上线；offline - 交易对已下线，不可交易；suspend -- 交易暂停；pre-online - 即将上线 |
| value-precision            | true     | integer  | 交易对交易金额的精度（小数点后位数），市价买入金额使用       |
| min-order-amt              | true     | float    | 交易对限价单最小下单量 ，以基础币种为单位（即将废弃）        |
| max-order-amt              | true     | float    | 交易对限价单最大下单量 ，以基础币种为单位（即将废弃）        |
| limit-order-min-order-amt  | true     | float    | 交易对限价单最小下单量 ，以基础币种为单位（NEW）             |
| limit-order-max-order-amt  | true     | float    | 交易对限价单最大下单量 ，以基础币种为单位（NEW）             |
| sell-market-min-order-amt  | true     | float    | 交易对市价卖单最小下单量，以基础币种为单位（NEW）            |
| sell-market-max-order-amt  | true     | float    | 交易对市价卖单最大下单量，以基础币种为单位（NEW）            |
| buy-market-max-order-value | true     | float    | 交易对市价买单最大下单金额，以计价币种为单位（NEW）          |
| min-order-value            | true     | float    | 交易对限价单和市价买单最小下单金额 ，以计价币种为单位        |
| max-order-value            | false    | float    | 交易对限价单和市价买单最大下单金额 ，以折算后的USDT为单位（NEW） |


## 获取所有币种

此接口返回所有火币香港支持的币种。


```shell
curl "https://api.huobi.com.hk/v1/common/currencys"
```

### HTTP 请求

- GET `/v1/common/currencys`


### 请求参数

此接口不接受任何参数。


> Response:

```json
  "data": [
    "usdt",
    "eth",
    "etc"
  ]
```

### 返回字段


<aside class="notice">返回的“data”对象是一个字符串数组，每一个字符串代表一个支持的币种。</aside>
## APIv2 币链参考信息

此节点用于查询各币种及其所在区块链的静态参考信息（公共数据）

### HTTP 请求

- GET `/v2/reference/currencies`

```shell
curl "https://api.huobi.com.hk/v2/reference/currencies?currency=usdt"
```

### 请求参数

| 字段名称       | 是否必需 | 类型    | 字段描述   | 取值范围                                                     |
| -------------- | -------- | ------- | ---------- | ------------------------------------------------------------ |
| currency       | false    | string  | 币种       | btc, ltc, bch, eth, etc ...(取值参考`GET /v1/common/currencys`) |
| authorizedUser | false    | boolean | 已认证用户 | true or false (如不填，缺省为true)                           |

> Response:

```json
{
    "code":200,
    "data":[
        {
            "chains":[
                {
                    "chain":"trc20usdt",
                    "displayName":"",
                    "baseChain": "TRX",
                    "baseChainProtocol": "TRC20",
                    "isDynamic": false,
                    "depositStatus":"allowed",
                    "maxTransactFeeWithdraw":"1.00000000",
                    "maxWithdrawAmt":"280000.00000000",
                    "minDepositAmt":"100",
                    "minTransactFeeWithdraw":"0.10000000",
                    "minWithdrawAmt":"0.01",
                    "numOfConfirmations":999,
                    "numOfFastConfirmations":999,
                    "withdrawFeeType":"circulated",
                    "withdrawPrecision":5,
                    "withdrawQuotaPerDay":"280000.00000000",
                    "withdrawQuotaPerYear":"2800000.00000000",
                    "withdrawQuotaTotal":"2800000.00000000",
                    "withdrawStatus":"allowed"
                },
                {
                    "chain":"usdt",
                    "displayName":"",
                    "baseChain": "BTC",
                    "baseChainProtocol": "OMNI",
                    "isDynamic": false,
                    "depositStatus":"allowed",
                    "maxWithdrawAmt":"19000.00000000",
                    "minDepositAmt":"0.0001",
                    "minWithdrawAmt":"2",
                    "numOfConfirmations":30,
                    "numOfFastConfirmations":15,
                    "transactFeeRateWithdraw":"0.00100000",
                    "withdrawFeeType":"ratio",
                    "withdrawPrecision":7,
                    "withdrawQuotaPerDay":"90000.00000000",
                    "withdrawQuotaPerYear":"111000.00000000",
                    "withdrawQuotaTotal":"1110000.00000000",
                    "withdrawStatus":"allowed"
                },
                {
                    "chain":"usdterc20",
                    "displayName":"",
                    "baseChain": "ETH",
                    "baseChainProtocol": "ERC20",
                    "isDynamic": false,
                    "depositStatus":"allowed",
                    "maxWithdrawAmt":"18000.00000000",
                    "minDepositAmt":"100",
                    "minWithdrawAmt":"1",
                    "numOfConfirmations":999,
                    "numOfFastConfirmations":999,
                    "transactFeeWithdraw":"0.10000000",
                    "withdrawFeeType":"fixed",
                    "withdrawPrecision":6,
                    "withdrawQuotaPerDay":"180000.00000000",
                    "withdrawQuotaPerYear":"200000.00000000",
                    "withdrawQuotaTotal":"300000.00000000",
                    "withdrawStatus":"allowed"
                }
            ],
            "currency":"usdt",
            "instStatus":"normal"
        }
        ]
}

```

### 响应数据


| 字段名称                | 是否必需 | 数据类型 | 字段描述                                                     | 取值范围               |
| ----------------------- | -------- | -------- | ------------------------------------------------------------ | ---------------------- |
| code                    | true     | int      | 状态码                                                       |                        |
| message                 | false    | string   | 错误描述（如有）                                             |                        |
| data                    | true     | object   |                                                              |                        |
| { currency              | true     | string   | 币种                                                         |                        |
| { chains                | true     | object   |                                                              |                        |
| chain                   | true     | string   | 链名称                                                       |                        |
| displayName             | true     | string   | 链显示名称                                                   |                        |
| baseChain               | false    | string   | 底层链名称                                                   |                        |
| baseChainProtocol       | false    | string   | 底层链协议                                                   |                        |
| isDynamic               | false    | boolean  | 是否动态手续费（仅对固定类型有效，withdrawFeeType=fixed）    | true,false             |
| numOfConfirmations      | true     | int      | 安全上账所需确认次数（达到确认次数后允许提币）               |                        |
| numOfFastConfirmations  | true     | int      | 快速上账所需确认次数（达到确认次数后允许交易但不允许提币）   |                        |
| minDepositAmt           | true     | string   | 单次最小充币金额                                             |                        |
| depositStatus           | true     | string   | 充币状态                                                     | allowed,prohibited     |
| minWithdrawAmt          | true     | string   | 单次最小提币金额                                             |                        |
| maxWithdrawAmt          | true     | string   | 单次最大提币金额                                             |                        |
| withdrawQuotaPerDay     | true     | string   | 当日提币额度（新加坡时区）                                   |                        |
| withdrawQuotaPerYear    | true     | string   | 当年提币额度                                                 |                        |
| withdrawQuotaTotal      | true     | string   | 总提币额度                                                   |                        |
| withdrawPrecision       | true     | int      | 提币精度                                                     |                        |
| withdrawFeeType         | true     | string   | 提币手续费类型（特定币种在特定链上的提币手续费类型唯一）     | fixed,circulated,ratio |
| transactFeeWithdraw     | false    | string   | 单次提币手续费（仅对固定类型有效，withdrawFeeType=fixed）    |                        |
| minTransactFeeWithdraw  | false    | string   | 最小单次提币手续费（仅对区间类型和有下限的比例类型有效，withdrawFeeType=circulated or ratio） |                        |
| maxTransactFeeWithdraw  | false    | string   | 最大单次提币手续费（仅对区间类型和有上限的比例类型有效，withdrawFeeType=circulated or ratio） |                        |
| transactFeeRateWithdraw | false    | string   | 单次提币手续费率（仅对比例类型有效，withdrawFeeType=ratio）  |                        |
| withdrawStatus}         | true     | string   | 提币状态                                                     | allowed,prohibited     |
| instStatus }            | true     | string   | 币种状态                                                     | normal,delisted        |

### 状态码

| 状态码 | 错误信息                            | 错误场景描述 |
| ------ | ----------------------------------- | ------------ |
| 200    | success                             | 请求成功     |
| 500    | error                               | 系统错误     |
| 2002   | invalid field value in "field name" | 非法字段取值 |

## 获取当前系统时间戳

此接口返回当前的系统时间戳，即从 **UTC** 1970年1月1日0时0分0秒0毫秒到现在的总**毫秒**数。

```shell
curl "https://api.huobi.com.hk/v1/common/timestamp"
```

### HTTP 请求

- GET `/v1/common/timestamp`

### 请求参数

此接口不接受任何参数。

> Response:

```json
  "data": 1494900087029
```

# 行情数据

## 简介

行情数据接口提供了多种K线、深度以及最新成交记录等行情数据。

行情接口提供的数据每1分钟更新一次


## K 线数据（蜡烛图）

此接口返回历史K线数据。K线周期以新加坡时间为基准开始计算，例如日K线的起始周期为新加坡时间0时至新加坡时间次日0时。

<aside class="notice">当前 REST API 不支持自定义时间区间，如需要历史固定时间范围的数据，请参考 Websocket API 中的 K 线接口。</aside>
<aside class="notice">获取 hb10 净值时， symbol 请填写 “hb10”。</aside>

```shell
curl "https://api.huobi.com.hk/market/history/kline?period=1day&size=200&symbol=btcusdt"
```

### HTTP 请求

- GET `/market/history/kline`

### 请求参数

| 参数   | 数据类型 | 是否必须 | 默认值 | 描述                                       | 取值范围                                                     |
| ------ | -------- | -------- | ------ | ------------------------------------------ | ------------------------------------------------------------ |
| symbol | string   | true     | NA     | 交易对                                     | btcusdt, ethbtc等|
| period | string   | true     | NA     | 返回数据时间粒度，也就是每根蜡烛的时间区间 | 1min, 5min, 15min, 30min, 60min, 4hour, 1day, 1mon, 1week, 1year |
| size   | integer  | false    | 150    | 返回 K 线数据条数                          | [1, 2000]                                                    |

> Response:

```json
[
  {
    "id": 1499184000,
    "amount": 37593.0266,
    "count": 0,
    "open": 1935.2000,
    "close": 1879.0000,
    "low": 1856.0000,
    "high": 1940.0000,
    "vol": 71031537.97866500
  }
]
```

### 响应数据

| 字段名称 | 数据类型 | 描述                                                    |
| -------- | -------- | ------------------------------------------------------- |
| id       | long     | 调整为新加坡时间的时间戳，单位秒，并以此作为此K线柱的id |
| amount   | float    | 以基础币种计量的交易量                                  |
| count    | integer  | 交易次数                                                |
| open     | float    | 本阶段开盘价                                            |
| close    | float    | 本阶段收盘价                                            |
| low      | float    | 本阶段最低价                                            |
| high     | float    | 本阶段最高价                                            |
| vol      | float    | 以报价币种计量的交易量                                  |



## 聚合行情（Ticker）

此接口获取ticker信息同时提供最近24小时的交易聚合信息。

```shell
curl "https://api.huobi.com.hk/market/detail/merged?symbol=ethusdt"
```
### HTTP 请求

- GET `/market/detail/merged`

### 请求参数

| 参数   | 数据类型 | 是否必须 | 默认值 | 描述   | 取值范围                                               |
| ------ | -------- | -------- | ------ | ------ | ------------------------------------------------------ |
| symbol | string   | true     | NA     | 交易对 | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |

> Response:

```json
{
  "id":1499225271,
  "ts":1499225271000,
  "close":1885.0000,
  "open":1960.0000,
  "high":1985.0000,
  "low":1856.0000,
  "amount":81486.2926,
  "count":42122,
  "vol":157052744.85708200,
  "ask":[1885.0000,21.8804],
  "bid":[1884.0000,1.6702]
}
```

### 响应数据

| 字段名称 | 数据类型 | 描述                                     |
| -------- | -------- | ---------------------------------------- |
| id       | long     | NA                                       |
| amount   | float    | 以基础币种计量的交易量（以滚动24小时计） |
| count    | integer  | 交易次数（以滚动24小时计）               |
| open     | float    | 本阶段开盘价（以滚动24小时计）           |
| close    | float    | 本阶段最新价（以滚动24小时计）           |
| low      | float    | 本阶段最低价（以滚动24小时计）           |
| high     | float    | 本阶段最高价（以滚动24小时计）           |
| vol      | float    | 以报价币种计量的交易量（以滚动24小时计） |
| bid      | object   | 当前的最高买价 [price, size]             |
| ask      | object   | 当前的最低卖价 [price, size]             |

## 所有交易对的最新 Tickers

获得所有交易对的 tickers。
```shell
curl "https://api.huobi.com.hk/market/tickers"
```
<aside class="notice">此接口返回所有交易对的 ticker，因此数据量较大。</aside>
### HTTP 请求

- GET `/market/tickers`

### 请求参数

此接口不接受任何参数。

> Response:

```json
[  
    {  
        "open":0.044297,      // 开盘价
        "close":0.042178,     // 收盘价
        "low":0.040110,       // 最低价
        "high":0.045255,      // 最高价
        "amount":12880.8510,  
        "count":12838,
        "vol":563.0388715740,
        "symbol":"ethbtc",
        "bid":0.007545,
        "bidSize":0.008,
        "ask":0.008088,
        "askSize":0.009
    },
    {  
        "open":0.008545,
        "close":0.008656,
        "low":0.008088,
        "high":0.009388,
        "amount":88056.1860,
        "count":16077,
        "vol":771.7975953754,
        "symbol":"ltcbtc",
        "bid":0.007545,
        "bidSize":0.008,
        "ask":0.008088,
        "askSize":0.009
    }
]
```

### 响应数据

核心响应数据为一个对象列，每个对象包含下面的字段

| 字段名称 | 数据类型 | 描述                                     |
| -------- | -------- | ---------------------------------------- |
| amount   | float    | 以基础币种计量的交易量（以滚动24小时计） |
| count    | integer  | 交易笔数（以滚动24小时计）               |
| open     | float    | 开盘价（以新加坡时间自然日计）           |
| close    | float    | 最新价（以新加坡时间自然日计）           |
| low      | float    | 最低价（以新加坡时间自然日计）           |
| high     | float    | 最高价（以新加坡时间自然日计）           |
| vol      | float    | 以报价币种计量的交易量（以滚动24小时计） |
| symbol   | string   | 交易对，例如btcusdt, ethbtc              |
| bid      | float    | 买一价                                   |
| bidSize  | float    | 买一量                                   |
| ask      | float    | 卖一价                                   |
| askSize  | float    | 卖一量                                   |

## 市场深度数据

此接口返回指定交易对的当前市场深度数据。

```shell
curl "https://api.huobi.com.hk/market/depth?symbol=btcusdt&type=step2"
```

### HTTP 请求

- GET `/market/depth`

### 请求参数

| 参数   | 数据类型 | 必须  | 默认值 | 描述                             | 取值范围                                               |      |
| ------ | -------- | ----- | ------ | -------------------------------- | ------------------------------------------------------ | ---- |
| symbol | string   | true  | NA     | 交易对                           | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |      |
| depth  | integer  | false | 20     | 返回深度的数量                   | 5，10，20                                              |      |
| type   | string   | true  | step0  | 深度的价格聚合度，具体说明见下方 | step0，step1，step2，step3，step4，step5               |      |

<aside class="notice">当type值为‘step0’时，‘depth’的默认值为150而非20。 </aside>
**参数type的各值说明**

| 取值  | 说明                    |
| ----- | ----------------------- |
| step0 | 无聚合                  |
| step1 | 聚合度为报价精度*10     |
| step2 | 聚合度为报价精度*100    |
| step3 | 聚合度为报价精度*1000   |
| step4 | 聚合度为报价精度*10000  |
| step5 | 聚合度为报价精度*100000 |

> Response:

```json
{
    "version": 31615842081,
    "ts": 1489464585407,
    "bids": [
      [7964, 0.0678], // [price, size]
      [7963, 0.9162],
      [7961, 0.1],
      [7960, 12.8898],
      [7958, 1.2],
      ...
    ],
    "asks": [
      [7979, 0.0736],
      [7980, 1.0292],
      [7981, 5.5652],
      [7986, 0.2416],
      [7990, 1.9970],
      ...
    ]
  }
```

### 响应数据

<aside class="notice">返回的JSON顶级数据对象名为'tick'而不是通常的'data'。</aside>

| 字段名称 | 数据类型 | 描述                               |
| -------- | -------- | ---------------------------------- |
| ts       | integer  | 调整为新加坡时间的时间戳，单位毫秒 |
| version  | integer  | 内部字段                           |
| bids     | object   | 当前的所有买单 [price, size]       |
| asks     | object   | 当前的所有卖单 [price, size]       |

## 最近市场成交记录

此接口返回指定交易对最新的一个交易记录。

```shell
curl "https://api.huobi.com.hk/market/trade?symbol=ethusdt"
```
### HTTP 请求

- GET `/market/trade`

### 请求参数

| 参数   | 数据类型 | 是否必须 | 默认值 | 描述                                                   |
| ------ | -------- | -------- | ------ | ------------------------------------------------------ |
| symbol | string   | true     | NA     | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |

> Response:

```json
{
    "id": 600848670,
    "ts": 1489464451000,
    "data": [
      {
        "id": 600848670,
        "trade-id": 102043494568,
        "price": 7962.62,
        "amount": 0.0122,
        "direction": "buy",
        "ts": 1489464451000
      }
    ]
}
```

### 响应数据

<aside class="notice">返回的JSON顶级数据对象名为'tick'而不是通常的'data'。</aside>

| 字段名称  | 数据类型 | 描述                                               |
| --------- | -------- | -------------------------------------------------- |
| id        | integer  | 唯一交易id（将被废弃）                             |
| trade-id  | integer  | 唯一成交ID（NEW）                                  |
| amount    | float    | 以基础币种为单位的交易量                           |
| price     | float    | 以报价币种为单位的成交价格                         |
| ts        | integer  | 调整为新加坡时间的时间戳，单位毫秒                 |
| direction | string   | 交易方向：“buy” 或 “sell”, “buy” 即买，“sell” 即卖 |

## 获得近期交易记录

此接口返回指定交易对近期的所有交易记录。

```shell
curl "https://api.huobi.com.hk/market/history/trade?symbol=ethusdt&size=2"
```
### HTTP 请求

- GET `/market/history/trade`

### 请求参数

| 参数   | 数据类型 | 是否必须 | 默认值 | 描述                                                   |
| ------ | -------- | -------- | ------ | ------------------------------------------------------ |
| symbol | string   | true     | NA     | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |
| size   | integer  | false    | 1      | 返回的交易记录数量，最大值2000                         |

> Response:

```json
[  
   {  
      "id":31618787514,
      "ts":1544390317905,
      "data":[  
         {  
            "amount":9.000000000000000000,
            "ts":1544390317905,
            "trade-id": 102043483472,
            "id":3161878751418918529341,
            "price":94.690000000000000000,
            "direction":"sell"
         },
         {  
            "amount":73.771000000000000000,
            "ts":1544390317905,
            "trade-id": 102043483473
            "id":3161878751418918532514,
            "price":94.660000000000000000,
            "direction":"sell"
         }
      ]
   },
   {  
      "id":31618776989,
      "ts":1544390311353,
      "data":[  
         {  
            "amount":1.000000000000000000,
            "ts":1544390311353,
            "trade-id": 102043494568,
            "id":3161877698918918522622,
            "price":94.710000000000000000,
            "direction":"buy"
         }
      ]
   }
]
```

### 响应数据

<aside class="notice">返回的数据对象是一个对象数组，每个数组元素为一个调整为新加坡时间的时间戳（单位毫秒）下的所有交易记录，这些交易记录以数组形式呈现。</aside>

| 参数      | 数据类型 | 描述                                               |
| --------- | -------- | -------------------------------------------------- |
| id        | integer  | 唯一交易id（将被废弃）                             |
| trade-id  | integer  | 唯一成交ID（NEW）                                  |
| amount    | float    | 以基础币种为单位的交易量                           |
| price     | float    | 以报价币种为单位的成交价格                         |
| ts        | integer  | 调整为新加坡时间的时间戳，单位毫秒                 |
| direction | string   | 交易方向：“buy” 或 “sell”, “buy” 即买，“sell” 即卖 |

## 最近24小时行情数据

此接口返回最近24小时的行情数据汇总。

<aside class="notice">此接口返回的成交量、成交金额为24小时滚动数据（平移窗口大小24小时），有可能会出现后一个窗口内的累计成交量、累计成交额小于前一窗口的情况。</aside>

```shell
curl "https://api.huobi.com.hk/market/detail?symbol=ethusdt"
```

### HTTP 请求

- GET `/market/detail`

### 请求参数

| 参数   | 数据类型 | 是否必须 | 默认值 | 描述                                                   |
| ------ | -------- | -------- | ------ | ------------------------------------------------------ |
| symbol | string   | true     | NA     | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |

> Response:

```json
{  
   "amount":613071.438479561,
   "open":86.21,
   "close":94.35,
   "high":98.7,
   "id":31619471534,
   "count":138909,
   "low":84.63,
   "version":31619471534,
   "vol":5.6617373443873316E7
}
```

### 响应数据

<aside class="notice">返回的JSON顶级数据对象名为'tick'而不是通常的'data'。</aside>

| 字段名称 | 数据类型 | 描述                                     |
| -------- | -------- | ---------------------------------------- |
| id       | integer  | 响应id                                   |
| amount   | float    | 以基础币种计量的交易量（以滚动24小时计） |
| count    | integer  | 交易次数（以滚动24小时计）               |
| open     | float    | 本阶段开盘价（以滚动24小时计）           |
| close    | float    | 本阶段收盘价（以滚动24小时计）           |
| low      | float    | 本阶段最低价（以滚动24小时计）           |
| high     | float    | 本阶段最高价（以滚动24小时计）           |
| vol      | float    | 以报价币种计量的交易量（以滚动24小时计） |
| version  | integer  | 内部数据                                 |



## 常见错误码

以下是行情数据接口返回的错误码、错误消息以及说明。

| 错误码            | 错误消息                            | 说明                    |
| ----------------- | ----------------------------------- | ----------------------- |
| invalid-parameter | invalid symbol                      | 无效的交易对            |
| invalid-parameter | invalid period                      | 请求K线，period参数错误 |
| invalid-parameter | invalid depth                       | 深度depth参数错误       |
| invalid-parameter | invalid type                        | 深度type 参数错误       |
| invalid-parameter | invalid size                        | size参数错误            |
| invalid-parameter | invalid size,valid range: [1, 2000] | size参数错误            |
| invalid-parameter | request timeout                     | 请求超时                |

# 账户相关

## 简介

账户相关接口提供了账户、余额、历史等查询以及资产划转等功能。

<aside class="notice">访问账户相关的接口需要进行签名认证。</aside>

## 账户信息 

API Key 权限：读取<br>
限频值（NEW）：100次/2s

查询当前用户的所有账户 ID `account-id` 及其相关信息

### HTTP 请求

- GET `/v1/account/accounts`

### 请求参数

无

> Response:

```json
{
  "data": [
    {
      "id": 100001,
      "type": "spot",
      "subtype": "",
      "state": "working"
    }
  ]
}
```

### 响应数据

| 参数名称 | 是否必须 | 数据类型 | 描述                               | 取值范围                                                     |
| -------- | -------- | -------- | ---------------------------------- | ------------------------------------------------------------ |
| id       | true     | long     | account-id                         |                                                              |
| state    | true     | string   | 账户状态                           | working：正常, lock：账户被锁定                              |
| type     | true     | string   | 账户类型                           | spot：交易账户 |
                               

## 资金账户余额

API Key 权限：读取<br>
限频值（NEW）：100次/2s

查询指定账户的余额，支持以下账户：资金账户

### HTTP 请求

- GET `/v2/external/account/account/balance`

### 请求参数

| 参数名称   | 是否必须 | 类型   | 描述                                                         | 默认值 | 取值范围 |
| ---------- | -------- | ------ | ------------------------------------------------------------ | ------ | -------- |
| types | true     | string | 账户类别，（可以传多个逗号隔开） |        |    asset：资金账户      |
| currency | true     | string | 币种，（可以传多个逗号隔开） |        |    取值参考 `GET /v1/account/accounts`      |

> Response:

```json
{
  "code": 200,
  "data": [
    {
      "balance": "1000.00",
      "suspense": "10.00",
      "type": "asset",
      "state": "normal",
      "currency": "usd"
    }
  ],
  "message": ""
}
```

### 响应数据

| 参数名称 | 是否必须 | 数据类型 | 描述     | 取值范围                                                     |
| -------- | -------- | -------- | -------- | ------------------------------------------------------------ |
| code       | false     | integer     | 响应状态码  |                                                              |
| data    | true     | object    |  |                              |
| { balance     | true     | string   | 可用余额 | |
| currency     | true     | string   | 币种 | |
| state     | true     | string   | 账户状态 | normal：正常，locked：冻结|
| suspense     | true     | string   | 冻结余额 | |
| type }     | true     | string   | 账户类别 | asset：资金账户|
| message     | false     | string   | 错误信息（英文） | |


## 交易账户余额

API Key 权限：读取<br>
限频值（NEW）：100次/2s

查询指定账户的余额，支持以下账户：

spot：交易账户

### HTTP 请求

- GET `/v1/account/accounts/{account-id}/balance`

### 请求参数

| 参数名称   | 是否必须 | 类型   | 描述                                                         | 默认值 | 取值范围 |
| ---------- | -------- | ------ | ------------------------------------------------------------ | ------ | -------- |
| account-id | true     | string | account-id，填在 path 中，取值参考 `GET /v1/account/accounts` |        |          |

> Response:

```json
{
  "data": {
    "id": 100009,
    "type": "spot",
    "state": "working",
    "list": [
      {
        "currency": "usdt",
        "type": "trade",
        "balance": "5007.4362872650"
      },
      {
        "currency": "usdt",
        "type": "frozen",
        "balance": "348.1199920000"
      }
    ]
  }
}
```

### 响应数据

| 参数名称 | 是否必须 | 数据类型 | 描述     | 取值范围                                                     |
| -------- | -------- | -------- | -------- | ------------------------------------------------------------ |
| id       | true     | long     | 账户 ID  |                                                              |
| state    | true     | string   | 账户状态 | working：正常  lock：账户被锁定                              |
| type     | true     | string   | 账户类型 | spot：交易账户|




## 资产划转

API Key 权限：交易<br>

该节点为母用户和子用户进行资产划转的通用接口。<br>

母用户和子用户均支持的功能包括：<br>
1、交易账户与资金账户之间的划转；<br>


其他划转功能将逐步上线，敬请期待。<br>

### HTTP 请求

- POST `/v1/account/transfer`

### 请求参数

| 参数              | 是否必填 | 数据类型 | 说明                                | 取值范围                         |
| ----------------- | -------- | -------- | ----------------------------------- | -------------------------------- |
| from-user         | true     | long     | 转出用户uid                         | 母用户uid,子用户uid              |
| from-account-type | true     | string   | 转出账户类型                        | spot                      |
| from-account      | true     | long     | 转出账户id                          |                                  |
| to-user           | true     | long     | 转入用户uid                         | 母用户uid,子用户uid              |
| to-account-type   | true     | string   | 转入账户类型                        | spot                      |
| to-account        | true     | long     | 转入账户id                          |                                  |
| currency          | true     | string   | 币种，即btc, ltc, bch, eth, etc ... | 取值参考GET /v1/common/currencys |
| amount            | true     | string   | 划转金额                            |                                  |


> Response:

```json
{
    "status": "ok",
    "data": {
        "transact-id": 220521190,
        "transact-time": 1590662591832
    }
}
```



## 常见错误码

以下是账户相关接口返回的错误码、错误消息以及说明。

| 错误码 | 错误消息                                    | 说明                                                |
| ------ | ------------------------------------------- | --------------------------------------------------- |
| 500    | system error                                | 调用内部服务异常                                    |
| 1002   | forbidden                                   | 禁止操作，如用户入参中accountId与UID不一致          |
| 2002   | "invalid field value in `currency`"         | currency不符合正则规则^[a-z0-9]{2,10}$              |
| 2002   | "invalid field value in `transactTypes`"    | 变动类型transactTypes不是“transfer”                 |
| 2002   | "invalid field value in `sort`"             | 分页请求参数不是合法的"asc或desc"                   |
| 2002   | "value in `fromId` is not found in record”  | 未找到fromId                                        |
| 2002   | "invalid field value in `accountId`"        | 查询参数中accountId为空                             |
| 2002   | "value in `startTime` exceeded valid range" | 入参查询时间大于当前时间，或者距离当前时间超过180天 |
| 2002   | "value in `endTime` exceeded valid range")  | 查询结束时间小于开始时间，或者查询时间跨度大于10天  |

# 钱包（充提相关）

## 简介

充提相关接口提供了充币地址、提币地址、提币额度、充提记录等查询，以及提币、取消提币等功能。

<aside class="notice">访问充提相关的接口需要进行签名认证。</aside>

## 充币地址查询

此节点用于查询特定币种（IOTA除外）在其所在区块链中的充币地址，母子用户均可用

API Key 权限：读取<br>
限频值（NEW）：20次/2s

<aside class="notice"> 充币地址查询暂不支持IOTA币 </aside>

```shell
curl "https://api.huobi.com.hk/v2/account/deposit/address?currency=btc"
```

### HTTP 请求

- GET ` /v2/account/deposit/address`

### 请求参数

| 字段名称 | 是否必需 | 类型   | 字段描述 | 取值范围                                                     |
| -------- | -------- | ------ | -------- | ------------------------------------------------------------ |
| currency | true     | string | 币种     | btc, ltc, bch, eth, etc ...(取值参考`GET /v1/common/currencys`) |

> Response:

```json
{
    "code": 200,
    "data": [
        {
            "currency": "btc",
            "address": "1PSRjPg53cX7hMRYAXGJnL8mqHtzmQgPUs",
            "addressTag": "",
            "chain": "btc"
        }
    ]
}
```

### 响应数据


| 字段名称   | 是否必需 | 数据类型 | 字段描述         | 取值范围 |
| ---------- | -------- | -------- | ---------------- | -------- |
| code       | true     | int      | 状态码           |          |
| message    | false    | string   | 错误描述（如有） |          |
| data       | true     | object   |                  |          |
| {currency  | true     | string   | 币种             |          |
| address    | true     | string   | 充币地址         |          |
| addressTag | true     | string   | 充币地址标签     |          |
| chain }    | true     | string   | 链名称           |          |



## 提币地址查询

API Key 权限：读取<br>

该节点用于查询API key可用的提币地址，限母用户可用。<br>

<aside class="notice">用户需要先通过Web端添加提币地址，才可以通过接口查询到</aside>

### HTTP 请求

- GET `/v2/account/withdraw/address`

### 请求参数

| 参数名称 | 是否必须 | 类型   | 描述                                                   | 默认值                         | 取值范围                                                     |
| -------- | -------- | ------ | ------------------------------------------------------ | ------------------------------ | ------------------------------------------------------------ |
| currency | true     | string | 币种                                                   |                                | btc, ltc, bch, eth, etc ...(取值参考`GET /v1/common/currencys`) |
| chain    | false    | string | 链名称                                                 | 如不填，返回所有链的提币地址   |                                                              |
| note     | false    | string | 地址备注                                               | 如不填，返回所有备注的提币地址 |                                                              |
| limit    | false    | int    | 单页最大返回条目数量                                   | 100                            | [1,500]                                                      |
| fromId   | false    | long   | 起始编号（提币地址ID，仅在下页查询时有效，详细见备注） | NA                             |                                                              |
> Response:

```json
{
    "code": 200,
    "data": [
        {
            "currency": "usdt",
            "chain": "usdt",
            "note": "币安",
            "addressTag": "",
            "address": "15PrEcqTJRn4haLeby3gJJebtyf4KgWmSd"
        }
    ]
}
```

### 响应数据
| 参数名称   | 是否必须 | 数据类型 | 描述                                                         | 取值范围 |
| ---------- | -------- | -------- | ------------------------------------------------------------ | -------- |
| code       | true     | int      | 状态码                                                       |          |
| message    | false    | string   | 错误描述（如有）                                             |          |
| data       | true     | object   |                                                              |          |
| { currency | true     | string   | 币种                                                         |          |
| chain      | true     | string   | 链名称                                                       |          |
| note       | true     | string   | 地址备注                                                     |          |
| addressTag | false    | string   | 地址标签，如有                                               |          |
| address }  | true     | string   | 地址                                                         |          |
| nextId     | false    | long     | 下页起始编号（提币地址ID，仅在查询结果需要分页返回时，包含此字段，详细见备注） |          |

备注：<br>
仅当用户请求查询的数据条目超出单页限制（由“limit“字段设定）时，服务器才返回”nextId“字段。用户收到服务器返回的”nextId“后 –<br>
1）须知晓后续仍有数据未能在本页返回；<br>
2）如需继续查询下页数据，应再次请求查询并将服务器返回的“nextId”作为“fromId“，其它请求参数不变。<br>
3）作为数据库记录ID，“nextId”和“fromId”除了用来翻页查询外，无其它业务含义。<br>




## 充提记录

此节点用于查询充提记录。

API Key 权限：读取<br>
限频值（NEW）：20次/2s

### HTTP 请求

- GET `/v1/query/deposit-withdraw`

### 请求参数

| 参数名称 | 是否必须 | 类型   | 描述             | 默认值                                                       | 取值范围                                                     |
| -------- | -------- | ------ | ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| currency | false    | string | 币种             |                                                              | btc, ltc, bch, eth, etc ...(取值参考`GET /v1/common/currencys`) |
| type     | true     | string | 充值或提币       |                                                              | deposit 或 withdraw,子用户仅可用deposit                      |
| from     | false    | string | 查询起始 ID      | 缺省时，默认值direct相关。当direct为‘prev’时，from 为1 ，从旧到新升序返回；当direct为’next‘时，from为最新的一条记录的ID，从新到旧降序返回 |                                                              |
| size     | false    | string | 查询记录大小     | 100                                                          | 1-500                                                        |
| direct   | false    | string | 返回记录排序方向 | 缺省时，默认为“prev” （升序）                                | “prev” （升序）or “next” （降序）                            |

> Response:

```json
{
  "data":
    [
      {
        "id": 1171,
        "type": "deposit",
        "currency": "xrp",
        "tx-hash": "ed03094b84eafbe4bc16e7ef766ee959885ee5bcb265872baaa9c64e1cf86c2b",
        "amount": 7.457467,
        "address": "rae93V8d2mdoUQHwBDBdM4NHCMehRJAsbm",
        "address-tag": "100040",
        "fee": 0,
        "state": "safe",
        "created-at": 1510912472199,
        "updated-at": 1511145876575
      },
      ...
    ]
}
```

### 响应数据

| 参数名称    | 是否必须 | 数据类型 | 描述                                                         | 取值范围                                 |
| ----------- | -------- | -------- | ------------------------------------------------------------ | ---------------------------------------- |
| id          | true     | long     | 充币或者提币订单id，翻页查询时from参数取自此值               |                                          |
| type        | true     | string   | 类型                                                         | 'deposit', 'withdraw', 子用户仅有deposit |
| currency    | true     | string   | 币种                                                         |                                          |
| tx-hash     | true     | string   | 交易哈希。如果是“快速提币”，则提币不通过区块链，该值为空。   |                                          |
| chain       | true     | string   | 链名称                                                       |                                          |
| amount      | true     | float    | 个数                                                         |                                          |
| address     | true     | string   | 目的地址                                                     |                                          |
| address-tag | true     | string   | 地址标签                                                     |                                          |
| fee         | true     | float    | 手续费                                                       |                                          |
| state       | true     | string   | 状态                                                         | 状态参见下表                             |
| error-code  | false    | string   | 提币失败错误码，仅type为”withdraw“，且state为”reject“、”wallet-reject“和”failed“时有。 |                                          |
| error-msg   | false    | string   | 提币失败错误描述，仅type为”withdraw“，且state为”reject“、”wallet-reject“和”failed“时有。 |                                          |
| created-at  | true     | long     | 发起时间                                                     |                                          |
| updated-at  | true     | long     | 最后更新时间                                                 |                                          |


- 虚拟币充值状态定义：

| 状态       | 描述                                 |
| ---------- | ------------------------------------ |
| unknown    | 状态未知                             |
| confirming | 区块确认中                           |
| confirmed  | 区块已完成，已经上账，可以划转和交易 |
| safe       | 区块已确认，可以提币                 |
| orphan     | 区块已被孤立                         |

- 虚拟币提币状态定义：

| 状态            | 描述         |
| --------------- | ------------ |
| verifying       | 待验证       |
| failed          | 验证失败     |
| submitted       | 已提交       |
| reexamine       | 审核中       |
| canceled        | 已撤销       |
| pass            | 审批通过     |
| reject          | 审批拒绝     |
| pre-transfer    | 处理中       |
| wallet-transfer | 已汇出       |
| wallet-reject   | 钱包拒绝     |
| confirmed       | 区块已确认   |
| confirm-error   | 区块确认错误 |
| repealed        | 已撤销       |

## 常见错误码

以下是充提相关接口返回的返回码、返回消息以及说明。

| 返回码 | 返回消息                             | 说明         |
| ------ | ------------------------------------ | ------------ |
| 200    | success                              | 请求成功     |
| 500    | error                                | 系统错误     |
| 1002   | unauthorized                         | 未授权       |
| 1003   | invalid signature                    | 验签失败     |
| 2002   | invalid field value in "field name"  | 非法字段取值 |
| 2003   | missing mandatory field "field name" | 强制字段缺失 |

## 常见问题

### Q1：为什么创建提币时返回api-not-support-temp-addr错误？
A：因安全考虑，API创建提币时仅支持已在提币地址列表中的地址，暂不支持使用API添加地址至提币地址列表中，需在网页端或APP端添加地址后才可在API中进行提币操作。

### Q2：为什么USDT提币时返回Invaild-Address错误？
A：USDT币种为典型的一币多链币种， 创建提币订单时应填写chain参数对应地址类型。以下表格展示了链和chain参数的对应关系：

| 链             | chain 参数 |
| -------------- | ---------- |
| ERC20 （默认） | usdterc20  |
| OMNI           | usdt       |
| TRX            | trc20usdt  |

如果chain参数为空，则默认的链为ERC20，或者也可以显示将参数赋值为`usdterc20`。

如果要提币到OMNI或者TRX，则chain参数应该填写usdt或者trc20usdt。chain参数可使用值请参考 `GET /v2/reference/currencies` 接口。

### Q3：创建提币时fee字段应该怎么填？
A：请参考 GET /v2/reference/currencies接口返回值，返回信息中withdrawFeeType为提币手续费类型，根据类型选择对应字段设置提币手续费。 

提币手续费类型包含：  

- transactFeeWithdraw : 单次提币手续费（仅对固定类型有效，withdrawFeeType=fixed）  
- minTransactFeeWithdraw : 最小单次提币手续费（仅对区间类型有效，withdrawFeeType=circulated or ratio） 
- maxTransactFeeWithdraw : 最大单次提币手续费（仅对区间类型和有上限的比例类型有效，withdrawFeeType=circulated or ratio
- transactFeeRateWithdraw :  单次提币手续费率（仅对比例类型有效，withdrawFeeType=ratio）

### Q4：如何查看我的提币额度？
A：请参考/v2/account/withdraw/quota接口返回值，返回信息中包含您查询币种的单次、当日提币额度以及剩余额度的信息。 

若您有大额提币需求，且提币数额超出相关限额，可联系官方客服进行沟通。  

# 子用户管理

## 简介

子用户管理接口提供了子用户的查询、权限设置、转账，子用户API Key的创建、修改、查询、删除，子用户充提地址、余额的查询等功能。

<aside class="notice">访问子用户管理的相关接口需要进行签名认证。</aside>



## 获取子用户列表

母用户通过此接口可获取所有子用户的UID列表及各用户状态

API Key 权限：读取

### HTTP 请求

- GET `/v2/sub-user/user-list`

### 请求参数

| 参数名称 | 是否必须 | 类型 | 描述                             | 默认值 | 取值范围 |
| -------- | -------- | ---- | -------------------------------- | ------ | -------- |
| fromId   | FALSE    | long | 查询起始编号（仅对翻页查询有效） |        |          |

> Response:

```json
{
    "code": 200,
    "data": [
        {
            "uid": 63628520,
            "userState": "normal"
        },
        {
            "uid": 132208121,
            "userState": "normal"
        }
    ]
}
```

### 响应数据

| 参数名称    | 是否必须 | 数据类型 | 描述                                       | 取值范围     |
| ----------- | -------- | -------- | ------------------------------------------ | ------------ |
| code        | TRUE     | int      | 状态码                                     |              |
| message     | FALSE    | string   | 错误描述（如有）                           |              |
| data        | TRUE     | object   |                                            |              |
| { uid       | TRUE     | long     | 子用户UID                                  |              |
| userState } | TRUE     | string   | 子用户状态                                 | lock, normal |
| nextId      | FALSE    | long     | 下页查询起始编号（仅在存在下页数据时返回） |              |

##冻结/解冻子用户

API Key 权限：交易<br>
限频值（NEW）：20次/2s

此接口用于母用户对其下一个子用户进行冻结和解冻操作

###HTTP 请求

- POST `/v2/sub-user/management`

### 请求参数

| 参数   | 是否必填 | 数据类型 | 长度 | 说明        | 取值范围                 |
| ------ | -------- | -------- | ---- | ----------- | ------------------------ |
| subUid | true     | long     | -    | 子用户的UID | -                        |
| action | true     | string   | -    | 操作类型    | lock(冻结)，unlock(解冻) |

> Response:

```json
{
  "code": 200,
	"data": {
     "subUid": 12902150,
     "userState":"lock"}
}
```

### 响应数据

| 参数      | 是否必填 | 数据类型 | 长度 | 说明       | 取值范围                   |
| --------- | -------- | -------- | ---- | ---------- | -------------------------- |
| subUid    | true     | long     | -    | 子用户UID  | -                          |
| userState | true     | string   | -    | 子用户状态 | lock(已冻结)，normal(正常) |


## 获取特定子用户的用户状态

母用户通过此接口可获取特定子用户的用户状态

API Key 权限：读取

### HTTP 请求

- GET `/v2/sub-user/user-state`

### 请求参数

| 参数名称 | 是否必须 | 类型 | 描述      | 默认值 | 取值范围 |
| -------- | -------- | ---- | --------- | ------ | -------- |
| subUid   | TRUE     | long | 子用户UID |        |          |

> Response:

```json
{
    "code": 200,
    "data": {
        "uid": 132208121,
        "userState": "normal"
    }
}
```

### 响应数据

| 参数名称    | 是否必须 | 数据类型 | 描述             | 取值范围     |
| ----------- | -------- | -------- | ---------------- | ------------ |
| code        | TRUE     | int      | 状态码           |              |
| message     | FALSE    | string   | 错误描述（如有） |              |
| data        | TRUE     | object   |                  |              |
| { uid       | TRUE     | long     | 子用户UID        |              |
| userState } | TRUE     | string   | 子用户状态       | lock, normal |



## 获取特定子用户的账户列表

母用户通过此接口可获取特定子用户的Account ID列表及各账户状态

API Key 权限：读取

### HTTP 请求

- GET `/v2/sub-user/account-list`

### 请求参数

| 参数名称 | 是否必须 | 类型 | 描述      | 默认值 | 取值范围 |
| -------- | -------- | ---- | --------- | ------ | -------- |
| subUid   | TRUE     | long | 子用户UID |        |          |

> Response:

```json
{
    "code": 200,
    "data": {
        "uid": 132208121,
        "deductMode": "sub",
        "list": [
            {
                "accountType": "spot",
                "activation": "activated",
                "transferrable": true,
                "accountIds": [
                    {
                        "accountId": 12255180,
                        "accountStatus": "normal"
                    }
                ]
            }
        ]
    }
}
```

### 响应数据

| 参数名称          | 是否必须 | 数据类型 | 描述                                              | 取值范围                                          |
| ----------------- | -------- | -------- | ------------------------------------------------- | ------------------------------------------------- |
| code              | TRUE     | int      | 状态码                                            |                                                   |
| message           | FALSE    | string   | 错误描述（如有）                                  |                                                   |
| data              | TRUE     | object   |                                                   |                                                   |
| { uid             | TRUE     | long     | 子用户UID                                         |                                                   |
| deductMode        | TRUE     |          |                                                   |                                                   |
| list              | TRUE     | object   |                                                   |                                                   |
| { accountType     | TRUE     | string   | 账户类型                                          | spot |
| activation        | TRUE     | string   | 账户激活状态                                      | activated, deactivated                            |
| transferrable     | FALSE    | bool     | 可划转权限（仅对accountType=spot有效）            | true, false                                       |
| accountIds        | FALSE    | object   |                                                   |                                                   |
| { accountId       | TRUE     | string   | 账户ID                                            |                                                   |
| subType           | FALSE    | string   | 账户子类型 |                                                   |
| accountStatus }}} | TRUE     | string   | 账户状态                                          | normal, locked                                    |






## 子用户余额（汇总）

API Key 权限：读取<br>
限频值（NEW）：2次/2s

母用户查询其下所有子用户的各币种汇总余额

### HTTP请求

- GET `/v1/subuser/aggregate-balance`


### 请求参数

无

> Response:

```json
  "data": [
      {
        "currency": "eos",
        "type": "spot",
        "balance": "1954559.809500000000000000"
      },
      {
        "currency": "btc",
        "type": "spot",
        "balance": "0.000000000000000000"
      },
      {
        "currency": "usdt",
        "type": "spot",
        "balance": "2925209.411300000000000000"
      }
   ]
```

### 响应数据


| 参数 | 是否必填 | 数据类型 |  说明       | 取值范围                                                     |
| ---- | -------- | -------- | ---------- | ------------------------------------------------------------ |
| status   | true        | string     | 状态 | "OK" or "Error"                                                         |
|data| true        | list   | |      |
| currency | true        | string   | 币种        |  |
| type | true        | string   | 账户类型        |  spot：交易账户|
| balance | true        | string   | 交易账户余额（可用余额和冻结余额的总和）      |  |




## 子用户交易账户余额

API Key 权限：读取<br>
限频值（NEW）：20次/2s

母用户查询子用户各币种账户余额

### HTTP 请求

- GET `/v1/account/accounts/{sub-uid}`

### 请求参数

| 参数    | 是否必填 | 数据类型 | 长度 | 说明         | 取值范围 |      |
| ------- | -------- | -------- | ---- | ------------ | -------- | ---- |
| sub-uid | true     | long     | -    | 子用户的 UID | -        |      |

> Response:

```json
{
  "status": "ok",
	"data": [
    {
      "id": 9910049,
      "type": "spot",
      "list": 
      [
        {
          "currency": "btc",
          "type": "trade",
          "balance": "1.00"
        },
        {
          "currency": "eth",
          "type": "trade",
          "balance": "1934.00"
        }
      ]
    },
    {
      "id": 9910050,
      "type": "point",
      "list": []
    }
	]
}
```

### 响应数据


| 参数 | 是否必填 | 数据类型 | 长度 | 说明       | 取值范围                                                     |      |
| ---- | -------- | -------- | ---- | ---------- | ------------------------------------------------------------ | ---- |
| id   | -        | long     | -    | 子用户 UID | -                                                            |      |
| type | -        | string   | -    | 账户类型   | spot：交易账户|      |
| list | -        | object   | -    | -          | -                                                            |      |

- list
	
| 参数     | 是否必填 | 数据类型 | 长度 | 说明     | 取值范围                          |      |
| -------- | -------- | -------- | ---- | -------- | --------------------------------- | ---- |
| currency | -        | string   | -    | 币种     | -                                 |      |
| type     | -        | string   | -    | 账户类型 | trade：可用，frozen：冻结 |      |
| balance  | -        | decimal  | -    | 账户余额 | -                                 |      |

## 常见错误码

以下是子用户相关接口返回的返回码、返回消息以及说明。

| 错误码 | 返回消息                                                  | 说明                               |
| ------ | --------------------------------------------------------- | ---------------------------------- |
| 1002   | "forbidden"                                               | 禁止操作，如该账户不允许创建子用户 |
| 1003   | "unauthorized”                                            | 未认证                             |
| 2002   | invalid field value                                       | 参数错误                           |
| 2014   | number of sub account in the request exceeded valid range | 子账户数量达到限制                 |
| 2014   | number of api key in the request exceeded valid range     | API Key数量超过限制                |
| 2016   | invalid request while value specified in sub user states  | 冻结/解冻失败    |

# 现货交易

## 简介

现货交易接口提供了下单、撤单、订单查询、成交明细查询、手续费率查询等功能。

<aside class="notice">访问交易相关的接口需要进行签名认证。</aside>

以下是订单业务相关的字段说明

**订单类型 (type)**：订单类型由方向和行为类型组合而成，[方向]-[行为类型]

方向：

- buy: 买
- sell: 卖

行为类型：

- market：市价单，该类型订单仅需指定下单金额或下单数量，不需要指定价格，订单在进入撮合时，会直接与对手方进行成交，直至金额或数量低于最小成交金额或成交数量为止。
- limit：限价单，该类型订单需指定下单价格，下单数量。
- limit-maker：只做maker单，即限价挂单，该订单在进入撮合时，只能作为maker进入市场深度，若订单会作为taker成交，则会被直接取消。
- ioc：立即成交或取消（immediately or cancel），该订单在进入撮合后，若不能直接成交，则会被直接取消（部分成交后，剩余部分也会被取消）。
- limit-fok： 立即完全成交否则完全取消（fill or kill），该订单在进入撮合后，若不能立即完全成交，则会被完全取消。
- market-grid：网格交易市价单（暂不支持API下单）。
- limit-grid：网格交易限价单（暂不支持API下单）。
- stop-limit：止盈止损单，设置高于或低于市场价格的订单，当订单到达触发价格后，才会正式的进入撮合队列。该类型已被策略委托订单代替，请使用策略委托订单。

**订单来源 (source)**：

- spot-api：现货API交易


**订单状态 (state)**:

- created：已创建，该状态订单尚未进入撮合队列。
- submitted : 已挂单等待成交，该状态订单已进入撮合队列当中。
- partial-filled : 部分成交，该状态订单在撮合队列当中，订单的部分数量已经被市场成交，等待剩余部分成交。
- filled : 已成交。该状态订单不在撮合队列中，订单的全部数量已经被市场成交。
- partial-canceled : 部分成交撤销。该状态订单不在撮合队列中，此状态由partial-filled转化而来，订单数量有部分被成交，但是被撤销。
- canceling : 撤销中。该状态订单正在被撤销的过程中，因订单最终需在撮合队列中剔除才会被真正撤销，所以此状态为中间过渡态。
- canceled : 已撤销。该状态订单不在撮合订单中，此状态订单没有任何成交数量，且被成功撤销。

**相关ID**

- order-id : 订单的唯一编号

- client-order-id : 客户自定义ID，该ID在下单时传入，与下单成功后返回的order-id对应。

  client-order-id 时效：对于已完结状态订单，2小时内有效。（其他状态订单有效时间仍为8小时有效）即订单创建超过2h，将无法使用clientOrderId查询已完结状态订单，建议用户通过orderid进行查询。其中，已完结订单状态包括部分成交已撤销、已撤销和完全成交。

- 。 允许的字符包括字母(大小写敏感)、数字、下划线 (_)和横线(-)，最长64位

- match-id : 订单在撮合中的顺序编号

- trade-id : 成交的唯一编号

## 下单

API Key 权限：交易
限频值；100次/2s

发送一个新订单到火币以进行撮合。

### HTTP 请求

- POST ` /v1/order/orders/place`

> Request:

```json
{
  "account-id": "100009",
  "amount": "10.1",
  "price": "100.1",
  "source": "api",
  "symbol": "ethusdt",
  "type": "buy-limit",
  "client-order-id": "a0001"
}
```

### 请求参数

| 参数名称        | 数据类型 | 是否必需 | 默认值   | 描述                                                         |
| --------------- | -------- | -------- | -------- | ------------------------------------------------------------ |
| account-id      | string   | true     | NA       | 账户 ID，取值参考 `GET /v1/account/accounts`。现货交易使用 ‘spot’ 账户的 account-id |
| symbol          | string   | true     | NA       | 交易对,即btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |
| type            | string   | true     | NA       | 订单类型，包括buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker（说明见下文）, buy-stop-limit, sell-stop-limit, buy-limit-fok, sell-limit-fok, buy-stop-limit-fok, sell-stop-limit-fok |
| amount          | string   | true     | NA       | 订单交易量（市价买单为订单交易额）                           |
| price           | string   | false    | NA       | 订单价格（对市价单无效）                                     |
| source          | string   | false    | spot-api | 现货交易填写“spot-api” |
| client-order-id | string   | false    | NA       | 用户自编订单号（最大长度64个字符，须在8小时内保持唯一性）    |
| stop-price      | string   | false    | NA       | 止盈止损订单触发价格                                         |
| operator        | string   | false    | NA       | 止盈止损订单触发价运算符 gte – greater than and equal (>=), lte – less than and equal (<=) |


**buy-limit-maker**

当“下单价格”>=“市场最低卖出价”，订单提交后，系统将拒绝接受此订单；

当“下单价格”<“市场最低卖出价”，提交成功后，此订单将被系统接受。

**sell-limit-maker**

当“下单价格”<=“市场最高买入价”，订单提交后，系统将拒绝接受此订单；

当“下单价格”>“市场最高买入价”，提交成功后，此订单将被系统接受。

> Response:

```json
{  
  "data": "59378"
}
```

### 响应数据

返回的主数据对象是一个对应下单单号的字符串。

如client order ID（在8小时内）被复用，节点将返回错误消息invalid.client.order.id。

## 批量下单

API Key 权限：交易<br>
限频值（NEW）：50次/2s<br>

一个批量最多10张订单

### HTTP 请求

- POST ` /v1/order/batch-orders`

> Request:

```json
[
	{
    "account-id": "123456",
    "price": "7801",
    "amount": "0.001",
    "symbol": "btcusdt",
    "type": "sell-limit",
    "client-order-id": "c1"
	},
	{
    "account-id": "123456",
    "price": "7802",
    "amount": "0.001",
    "symbol": "btcusdt",
    "type": "sell-limit",
    "client-order-id": "d2"
	}
]
```

### 请求参数

| 参数名称        | 数据类型 | 是否必需 | 默认值   | 描述                                                         |
| --------------- | -------- | -------- | -------- | ------------------------------------------------------------ |
| [{ account-id   | string   | true     | NA       | 账户 ID，取值参考 `GET /v1/account/accounts`。现货交易使用 ‘spot’ 账户的 account-id |
| symbol          | string   | true     | NA       | 交易对,即btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |
| type            | string   | true     | NA       | 订单类型，包括buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker（说明见下文）, buy-stop-limit, sell-stop-limit, buy-limit-fok, sell-limit-fok, buy-stop-limit-fok, sell-stop-limit-fok |
| amount          | string   | true     | NA       | 订单交易量（市价买单为订单交易额）                           |
| price           | string   | false    | NA       | 订单价格（对市价单无效）                                     |
| source          | string   | false    | spot-api | 现货交易填写“spot-api”|
| client-order-id | string   | false    | NA       | 用户自编订单号（最大长度64个字符，须在8小时内保持唯一性）    |
| stop-price      | string   | false    | NA       | 止盈止损订单触发价格                                         |
| operator }]     | string   | false    | NA       | 止盈止损订单触发价运算符 gte – greater than and equal (>=), lte – less than and equal (<=) |

**buy-limit-maker**

当“下单价格”>=“市场最低卖出价”，订单提交后，系统将拒绝接受此订单；

当“下单价格”<“市场最低卖出价”，提交成功后，此订单将被系统接受。

**sell-limit-maker**

当“下单价格”<=“市场最高买入价”，订单提交后，系统将拒绝接受此订单；

当“下单价格”>“市场最高买入价”，提交成功后，此订单将被系统接受。

> Response:

```json
{
    "status": "ok",
    "data": [
        {
            "order-id": 61713400772,
            "client-order-id": "c1"
        },
        {
            "order-id": 61713400940,
            "client-order-id": "d2"
        }
    ]
}
```

### 响应数据

| 字段名称        | 数据类型 | 描述                                 |
| --------------- | -------- | ------------------------------------ |
| [{ order-id     | integer  | 订单编号                             |
| client-order-id | string   | 用户自编订单号（如有）               |
| err-code        | string   | 订单被拒错误码（仅对被拒订单有效）   |
| err-msg }]      | string   | 订单被拒错误信息（仅对被拒订单有效） |

如client order ID（在8小时内）被复用，节点返回先前订单的order ID及client order ID。

## 撤销订单

API Key 权限：交易<br>
限频值（NEW）：100次/2s

此接口发送一个撤销订单的请求。

<aside class="warning">此接口只提交取消请求，实际取消结果需要通过订单状态，撮合状态等接口来确认。</aside>
### HTTP 请求

- POST ` /v1/order/orders/{order-id}/submitcancel`


### 请求参数

| 参数名称 | 是否必须 | 类型   | 描述               | 默认值 | 取值范围 |
| -------- | -------- | ------ | ------------------ | ------ | -------- |
| order-id | true     | string | 订单ID，填在path中 |        |          |
| symbol | false     | string | 交易对，填在URL请求参数中 |        |          |

> Success response:

```json
{  
  "data": "59378"
}
```

### 响应数据

返回的主数据对象是一个对应下单单号的字符串。

### 错误码

> Failure response:

```json
{
  "status": "error",
  "err-code": "order-orderstate-error",
  "err-msg": "订单状态错误",
  "order-state":-1 // 当前订单状态
}
```

返回字段列表中，order-state的可能取值包括 -

| order-state | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| -1          | order was already closed in the long past (order state = canceled, partial-canceled, filled, partial-filled) |
| 1           | created                                             |
| 3           | submitted                                             |
| 4           | partial-filled                                             |
| 5           | partial-canceled                                             |
| 6           | filled                                                       |
| 7           | canceled                                                     |
| 10          | cancelling                                                   |

## 撤销订单（基于client order ID）

API Key 权限：交易<br>
限频值（NEW）：100次/2s

此接口基于client-order-id（8小时内有效）发送一个撤销订单的请求。

<aside class="notice">撤单个订单建议通过接口/v1/order/orders/{order-id}/submitcancel，会更快更稳定</aside>
<aside class="warning">此接口只提交取消请求，实际取消结果需要通过订单状态，撮合状态等接口来确认。</aside>



### HTTP 请求

- POST ` /v1/order/orders/submitCancelClientOrder`

> Request:

```json
{
  "client-order-id": "a0001"
}
```

### 请求参数

| 参数名称        | 是否必须 | 类型   | 描述                                                         | 默认值 | 取值范围 |
| --------------- | -------- | ------ | ------------------------------------------------------------ | ------ | -------- |
| client-order-id | true     | string | 用户自编订单号，必须8小时内已有该订单存在，否则下次下单时不允许用此值 |        |          |


> Response:

```json
{  
  "data": "10"
}
```
### 响应数据

| 字段名称 | 数据类型 | 描述       |
| -------- | -------- | ---------- |
| data     | integer  | 撤单状态码 |

| Status Code | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| -1          | order was already closed in the long past (order state = canceled, partial-canceled, filled, partial-filled) |
| 0           | client-order-id not found                                    |
| 1           | created                                             |
| 3           | submitted                                           |
| 4           | partial-filled                                      |
| 5           | partial-canceled                                             |
| 6           | filled                                                       |
| 7           | canceled                                                     |
| 10          | cancelling                                                   |



## 自动撤销订单

API Key 权限：交易<br>

为了防止API用户在发生网络故障或用户端系统故障与火币系统失去联系时，给用户造成意外损失，火币新增自动撤单接口，当用户与火币发生意外断连时，能自动帮用户取消全部委托单，以避免损失，即提供Dead man's switch功能。若开启，在设定的时间数完前，接口没有被再次调用，则用户所有现货委托单将被取消（最大支持撤500单）。

### HTTP 请求

- POST `/v2/algo-orders/cancel-all-after`

> Request:

```json
{
  "timeout": "10"
}
```

### 请求参数

| 参数名称        | 是否必须 | 类型   | 描述     | 默认值 | 取值范围 |
| --------------- | -------- | ------ | ----------- | ------ | -------- |
| timeout | true  | int | 超时时间（单位：秒），设置建议见附注 |  NA   |  0或者大于等于5秒  |


> 响应示例-开启成功 
Response:

```json
{
"code": 200,
"data": [
    {
       "currentTime":"1587971400",
       "triggerTime":"1587971460"
  }
]
}
```


> 响应示例-关闭成功 
Response:

```json
{
"code": 200,
"data": [
    {
       "currentTime":"1587971400",
       "triggerTime":"0"
  }
]
}
```


> 响应示例-开启/关闭失败
Response:

```json
{
"code": 2003,
"message": "missing mandatory field"
}
```
### 响应数据

| **参数名称**  | **是否必须** | **数据类型** | **描述**         |
| ------------- | ------------ | ------------ | ---------------- |
| code          | true         | int          | 状态码           |
| message       | false        | string       | 错误描述（如有） |
| data          | true         | object       |                  |
| { currentTime | true         | long         | 当前时间         |
| triggerTime } | true         | long         | 触发时间         |


## 查询当前未成交订单

API Key 权限：读取<br>
限频值（NEW）：50次/2s

查询已提交但是仍未完全成交或未被撤销的订单。

### HTTP 请求

- GET `/v1/order/openOrders`

> Request:

```json
{
   "account-id": "100009",
   "symbol": "ethusdt",
   "side": "buy"
}
```

### 请求参数

| 参数名称   | 数据类型 | 是否必需                                         | 默认值 | 描述                                                         |
| ---------- | -------- | ------------------------------------------------ | ------ | ------------------------------------------------------------ |
| account-id | string   | true                                             | NA     | 账户 ID，取值参考 `GET /v1/account/accounts`。现货交易使用‘spot’账户的 account-id |
| symbol     | string   | ture                                             | NA     | 交易对,即btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |
| side       | string   | false                                            | both   | 指定只返回某一个方向的订单，可能的值有: buy, sell. 默认两个方向都返回。 |
| types      | string | false |        |查询的订单类型组合，使用逗号分割|
| from       | string   | false                                            |        | 查询起始 ID，如果是向后查询，则赋值为上一次查询结果中得到的最后一条id ；如果是向前查询，则赋值为上一次查询结果中得到的第一条id |
| direct     | string   | false (如字段'from'已设定，此字段'direct'为必填) |        | 查询方向，prev 向前；next 向后                               |
| size       | int      | false                                            | 100    | 返回订单的数量，最大值500。                                  |

> Response:

```json
{  
  "data": [
    {
      "id": 5454937,
      "symbol": "ethusdt",
      "account-id": 30925,
      "amount": "1.000000000000000000",
      "price": "0.453000000000000000",
      "created-at": 1530604762277,
      "type": "sell-limit",
      "filled-amount": "0.0",
      "filled-cash-amount": "0.0",
      "filled-fees": "0.0",
      "source": "web",
      "state": "submitted"
    }
  ]
}
```

### 响应数据

| 字段名称           | 数据类型 | 描述                                                   |
| ------------------ | -------- | ------------------------------------------------------ |
| id                 | integer  | 订单id，无大小顺序，可作为下一次翻页查询请求的from字段 |
| client-order-id    | string   | 用户自编订单号（所有open订单可返回client-order-id）    |
| symbol             | string   | 交易对, 例如btcusdt, ethbtc                            |
| price              | string   | limit order的交易价格                                  |
| created-at         | int      | 订单创建的调整为新加坡时间的时间戳，单位毫秒           |
| type               | string   | 订单类型                                               |
| filled-amount      | string   | 订单中已成交部分的数量                                 |
| filled-cash-amount | string   | 订单中已成交部分的总价格                               |
| filled-fees        | string   | 已交交易手续费总额（准确数值请参考matchresults接口）   |
| source             | string   | 订单来源                                               |
| state              | string   | 订单状态，包括created, submitted, partial-filled       |
| stop-price         | string   | 止盈止损订单触发价格                                   |
| operator           | string   | 止盈止损订单触发价运算符                               |

## 批量撤销所有订单

API Key 权限：交易<br>
限频值（NEW）：50次/2s

此接口发送批量撤销所有（单次最大100个）订单的请求。

<aside class="warning">此接口只提交取消请求，实际取消结果需要通过订单状态，撮合状态等接口来确认。</aside>
### HTTP 请求

- POST ` /v1/order/orders/batchCancelOpenOrders`


### 请求参数

| 参数名称   | 是否必须 | 类型   | 描述                                                         | 默认值 | 取值范围                                          |
| ---------- | -------- | ------ | ------------------------------------------------------------ | ------ | ------------------------------------------------- |
| account-id | false    | string | 账户ID，取值参考 `GET /v1/account/accounts`                  |        |                                                   |
| symbol     | false    | string | 交易代码列表（最多10 个symbols，多个交易代码间以逗号分隔），btcusdt, ethbtc...（取值参考`/v1/common/symbols`） | all    |                                                   |
| types      | false    | string | 订单类型组合，使用逗号分割                                   |        | 所有可能的订单类型（见本章节简介）                |
| side       | false    | string | 主动交易方向                                                 |        | “buy”或“sell”，缺省将返回所有符合条件尚未成交订单 |
| size       | false    | int    | 撤销订单的数量                                               | 100    | [0,100]                                           |


> Response:

```json
{
  "status": "ok",
  "data": {
    "success-count": 2,
    "failed-count": 0,
    "next-id": 5454600
  }
}
```


### 响应数据


| 参数名称      | 是否必须 | 数据类型 | 描述                   |
| ------------- | -------- | -------- | ---------------------- |
| success-count | true     | int      | 成功取消的订单数       |
| failed-count  | true     | int      | 取消失败的订单数       |
| next-id       | true     | long     | 下一个可以撤销的订单号，返回-1表示没有可以撤销的订单 |

## 批量撤销指定订单

API Key 权限：交易<br>
限频值（NEW）：50次/2s

此接口同时为多个订单（基于id）发送取消请求，建议通过order-ids来撤单，比client-order-ids更快更稳定。

### HTTP 请求

- POST ` /v1/order/orders/batchcancel`

> Request:

```json
{
  "client-order-ids": [
   "5983466", "5722939", "5721027", "5719487"
  ]
}
```

### 请求参数

| 参数名称         | 是否必须 | 类型     | 描述                                                         | 默认值 | 取值范围           |
| ---------------- | -------- | -------- | ------------------------------------------------------------ | ------ | ------------------ |
| order-ids        | false    | string[] | 订单编号列表（order-ids和client-order-ids必须且只能选一个填写，不超过50张订单），建议通过order-ids来撤单，比client-order-ids更快更稳定 |        | 单次不超过50个订单 |
| client-order-ids | false    | string[] | 用户自编订单号列表（order-ids和client-order-ids必须且只能选一个填写，不超过50张订单），必须已有该订单存在，否则下次下单时不允许用此值 |        | 单次不超过50个订单 |

> Response:

```json
{
    "status": "ok",
    "data": {
        "success": [
            "5983466"            
        ],
        "failed": [
            {
              "err-msg": "Incorrect order state",
              "order-state": 7,
              "order-id": "",
              "err-code": "order-orderstate-error",
              "client-order-id": "first"
            },
            {
              "err-msg": "Incorrect order state",
              "order-state": 7,
              "order-id": "",
              "err-code": "order-orderstate-error",
              "client-order-id": "second"
            },
            {
              "err-msg": "The record is not found.",
              "order-id": "",
              "err-code": "base-not-found",
              "client-order-id": "third"
            }
          ]
    }
}
```

### 响应数据

| 字段名称  | 数据类型 | 描述                                                         |
| --------- | -------- | ------------------------------------------------------------ |
| { success | string[] | 撤单成功订单列表（可为order-id列表或client-order-id列表，以用户请求为准） |
| failed }  | string[] | 撤单失败订单列表（可为order-id列表或client-order-id列表，以用户请求为准） |

撤单失败订单列表 -

| 字段名称        | 数据类型 | 描述                                                         |
| --------------- | -------- | ------------------------------------------------------------ |
| [{ order-id     | string   | 订单编号（如用户创建订单时包含order-id，返回中也须包含此字段） |
| client-order-id | string   | 用户自编订单号（如用户创建订单时包含client-order-id，返回中也须包含此字段） |
| err-code        | string   | 订单被拒错误码（仅对被拒订单有效）                           |
| err-msg         | string   | 订单被拒错误信息（仅对被拒订单有效）                         |
| order-state }]  | string   | 当前订单状态（若有）                                         |

返回字段列表中，order-state的可能取值包括 -

| order-state | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| -1          | order was already closed in the long past (order state = canceled, partial-canceled, filled, partial-filled) |
| 1           | created                                             |
| 3           | submitted                                             |
| 4           | partial-filled                                      |
| 5           | partial-canceled                                             |
| 6           | filled                                                       |
| 7           | canceled                                                     |
| 10          | cancelling                                                   |

## 查询订单详情

API Key 权限：读取<br>
限频值（NEW）：50次/2s

此接口返回指定订单的最新状态和详情。通过API创建的订单，撤销超过2小时后无法查询。通过API创建的订单返回order-id，按此order-id查询订单还是返回base-record-invalid是因为系统内部处理有延迟，但是不影响成交。建议您后续重试查询或者通过订阅订单推送WebSocket消息查询。

### HTTP 请求

- GET `/v1/order/orders/{order-id}`


### 请求参数

| 参数名称 | 是否必须 | 类型   | 描述               | 默认值 | 取值范围 |
| -------- | -------- | ------ | ------------------ | ------ | -------- |
| order-id | true     | string | 订单ID，填在path中 |        |          |


> Response:

```json
{  
  "data": 
  {
    "id": 59378,
    "symbol": "ethusdt",
    "account-id": 100009,
    "amount": "10.1000000000",
    "price": "100.1000000000",
    "created-at": 1494901162595,
    "type": "buy-limit",
    "field-amount": "10.1000000000",
    "field-cash-amount": "1011.0100000000",
    "field-fees": "0.0202000000",
    "finished-at": 1494901400468,
    "user-id": 1000,
    "source": "api",
    "state": "filled",
    "canceled-at": 0
  }
}
```

### 响应数据

| 字段名称          | 是否必须 | 数据类型 | 描述                                                         | 取值范围                           |
| ----------------- | -------- | -------- | ------------------------------------------------------------ | ---------------------------------- |
| account-id        | true     | long     | 账户 ID                                                      |                                    |
| amount            | true     | string   | 订单数量                                                     |                                    |
| canceled-at       | false    | long     | 订单撤销时间                                                 |                                    |
| created-at        | true     | long     | 订单创建时间                                                 |                                    |
| field-amount      | true     | string   | 已成交数量                                                   |                                    |
| field-cash-amount | true     | string   | 已成交总金额                                                 |                                    |
| field-fees        | true     | string   | 已成交手续费（准确数值请参考matchresults接口）               |                                    |
| finished-at       | false    | long     | 订单变为终结态的时间，不是成交时间，包含“已撤单”状态         |                                    |
| id                | true     | long     | 订单ID                                                       |                                    |
| client-order-id   | false    | string   | 用户自编订单号（所有open订单可返回client-order-id（如有）；仅7天内（基于订单创建时间）的closed订单（state <> canceled）可返回client-order-id（如有）；仅8小时内（基于订单创建时间）的closed订单（state = canceled）可返回client-order-id（如有）） |                                    |
| price             | true     | string   | 订单价格                                                     |                                    |
| source            | true     | string   | 订单来源                                                     | api                                |
| state             | true     | string   | 订单状态                                                     | 所有可能的订单状态（见本章节简介） |
| symbol            | true     | string   | 交易对                                                       | btcusdt, ethbtc, rcneth ...        |
| type              | true     | string   | 订单类型                                                     | 所有可能的订单类型（见本章节简介） |
| stop-price        | false    | string   | 止盈止损订单触发价格                                         |                                    |
| operator          | false    | string   | 止盈止损订单触发价运算符                                     | gte,lte                            |


## 查询订单详情（基于client order ID）

API Key 权限：读取<br>
限频值（NEW）：50次/2s

此接口返回指定用户自编订单号（8小时内）的订单最新状态和详情。通过API创建的订单，撤销超过2小时后无法查询。建议通过GET `/v1/order/orders/{order-id}`来撤单，比使用clientOrderId更快更稳定

### HTTP 请求

- GET `/v1/order/orders/getClientOrder`

### 请求参数

| 参数名称      | 是否必须 | 类型   | 描述           | 默认值 | 取值范围 |
| ------------- | -------- | ------ | -------------- | ------ | -------- |
| clientOrderId | true     | string | 用户自编订单号 |        |          |

> Response:

```json
{  
  "data": 
  {
    "id": 59378,
    "symbol": "ethusdt",
    "account-id": 100009,
    "amount": "10.1000000000",
    "price": "100.1000000000",
    "created-at": 1494901162595,
    "type": "buy-limit",
    "field-amount": "10.1000000000",
    "field-cash-amount": "1011.0100000000",
    "field-fees": "0.0202000000",
    "finished-at": 1494901400468,
    "user-id": 1000,
    "source": "api",
    "state": "filled",
    "canceled-at": 0
  }
}
```

### 响应数据

| 字段名称          | 是否必须 | 数据类型 | 描述                                                         | 取值范围                           |
| ----------------- | -------- | -------- | ------------------------------------------------------------ | ---------------------------------- |
| account-id        | true     | long     | 账户 ID                                                      |                                    |
| amount            | true     | string   | 订单数量                                                     |                                    |
| canceled-at       | false    | long     | 订单撤销时间                                                 |                                    |
| created-at        | true     | long     | 订单创建时间                                                 |                                    |
| field-amount      | true     | string   | 已成交数量                                                   |                                    |
| field-cash-amount | true     | string   | 已成交总金额                                                 |                                    |
| field-fees        | true     | string   | 已成交手续费（准确数值请参考matchresults接口）               |                                    |
| finished-at       | false    | long     | 订单变为终结态的时间，不是成交时间，包含“已撤单”状态         |                                    |
| id                | true     | long     | 订单ID                                                       |                                    |
| client-order-id   | false    | string   | 用户自编订单号（仅8小时内（基于订单创建时间）的订单可被查询，订单状态是终态的2小时内可查询） |                                    |
| price             | true     | string   | 订单价格                                                     |                                    |
| source            | true     | string   | 订单来源                                                     | api                                |
| state             | true     | string   | 订单状态                                                     | 所有可能的订单状态（见本章节简介） |
| symbol            | true     | string   | 交易对                                                       | btcusdt, ethbtc, rcneth ...        |
| type              | true     | string   | 订单类型                                                     | 所有可能的订单类型（见本章节简介） |
| stop-price        | false    | string   | 止盈止损订单触发价格                                         |                                    |
| operator          | false    | string   | 止盈止损订单触发价运算符                                     | gte,lte                            |

如client order ID不存在，返回如下错误信息 
{
    "status": "error",
    "err-code": "base-record-invalid",
    "err-msg": "record invalid",
    "data": null
}

## 成交明细

API Key 权限：读取<br>
限频值（NEW）：50次/2s

此接口返回指定订单的成交明细。

### HTTP 请求

- GET `/v1/order/orders/{order-id}/matchresults`

### 请求参数

| 参数名称 | 是否必须 | 类型   | 描述               | 默认值 | 取值范围 |
| -------- | -------- | ------ | ------------------ | ------ | -------- |
| order-id | true     | string | 订单ID，填在path中 |        |          |


> Response:

```json
{  
  "data": [
    {
      "id": 29553,
      "order-id": 59378,
      "match-id": 59335,
      "trade-id": 100282808529,
      "symbol": "ethusdt",
      "type": "buy-limit",
      "source": "api",
      "price": "100.1000000000",
      "filled-amount": "9.1155000000",
      "filled-fees": "0.0182310000",
      "created-at": 1494901400435,
      "role": "maker",
      "filled-points": "0.0",
      “fee-deduct-state”:"done",
      "fee-deduct-currency": ""
    }
    ...
  ]
}
```

### 响应数据

<aside class="notice">返回的主数据对象为一个对象数组，其中每一个元件代表一个交易结果。</aside>

| 字段名称            | 是否必须 | 数据类型 | 描述                                                         | 取值范围                                                     |
| ------------------- | -------- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| created-at          | true     | long     | 该成交记录创建的时间戳（略晚于成交时间）                     |                                                              |
| filled-amount       | true     | string   | 成交数量                                                     |                                                              |
| filled-fees         | true     | string   | 交易手续费（正值）或交易返佣金（负值）                       |                                                              |
| fee-currency        | true     | string   | 交易手续费或交易返佣币种（买单的交易手续费币种为基础币种，卖单的交易手续费币种为计价币种；买单的交易返佣币种为计价币种，卖单的交易返佣币种为基础币种） |                                                              |
| id                  | true     | long     | 订单成交记录ID                                               |                                                              |
| match-id            | true     | long     | 撮合ID，订单在撮合中执行的顺序ID                             |                                                              |
| order-id            | true     | long     | 订单ID，成交所属订单的ID                                     |                                                              |
| trade-id            | false    | integer  | Unique trade ID (NEW)唯一成交编号，成交时产生的唯一编号ID    |                                                              |
| price               | true     | string   | 成交价格                                                     |                                                              |
| source              | true     | string   | 订单来源                                                     | api                                                          |
| symbol              | true     | string   | 交易对                                                       | btcusdt, ethbtc, rcneth ...                                  |
| type                | true     | string   | 订单类型                                                     | 所有可能的订单类型（见本章节简介） |
| role                | true     | string   | 成交角色                                                     | maker,taker                                                  |
| filled-points       | true     | string   | 抵扣数量（可为ht）                                  |             ht                                                 |
| fee-deduct-currency | true     | string   | 抵扣类型                                                     | 如果为空，代表扣除的手续费是原币；如果为"ht"，代表抵扣手续费的是HT |
| fee-deduct-state    | true     | string   | 抵扣状态                                                     | 抵扣中：ongoing，抵扣完成：done                              |



## 搜索历史订单

API Key 权限：读取<br>
限频值（NEW）：50次/2s

此接口基于搜索条件查询历史订单。通过API创建的订单，撤销超过2小时后无法查询。

用户可选择以“时间范围”查询历史订单，以替代原先的以“日期范围“查询方式。

-	如用户填写start-time AND/OR end-time查询历史订单，服务器将按照用户指定的“时间范围“查询并返回，并忽略start-date/end-date参数。此方式的查询窗口大小限定为最大48小时，窗口平移范围为最近180天。

-	如用户不填写start-time/end-time参数，而填写start-date AND/OR end-date查询历史订单，服务器将按照用户指定的“日期范围“查询并返回。此方式的查询窗口大小限定为最大2天，窗口平移范围为最近180天。

-	如用户既不填写start-time/end-time参数，也不填写start-date/end-date参数，服务器将缺省以当前时间为end-time，返回最近48小时内的历史订单。

火币香港建议用户以“时间范围“查询历史订单。未来，火币香港将下线以”日期范围“查询历史订单的方式，并另行通知。


### HTTP 请求

- GET `/v1/order/orders`

> Request:

```json
{
   "account-id": "100009",
   "amount": "10.1",
   "price": "100.1",
   "source": "api",
   "symbol": "ethusdt",
   "type": "buy-limit"
}
```


### 请求参数

| 参数名称   | 是否必须 | 类型   | 描述                                                         | 默认值                      | 取值范围                                                     |
| ---------- | -------- | ------ | ------------------------------------------------------------ | --------------------------- | ------------------------------------------------------------ |
| symbol     | true     | string | 交易对                                                       |                             | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`）       |
| types      | false    | string | 查询的订单类型组合，使用逗号分割                             |                             | 所有可能的订单类型（见本章节简介）                           |
| start-time | false    | long   | 查询开始时间, 时间格式UTC time in millisecond。 以订单生成时间进行查询 | -48h 查询结束时间的前48小时 | 取值范围 [((end-time) – 48h), (end-time)] ，查询窗口最大为48小时，窗口平移范围为最近180天，已完全撤销的历史订单的查询窗口平移范围只有最近2小时(state="canceled") |
| end-time   | false    | long   | 查询结束时间, 时间格式UTC time in millisecond。 以订单生成时间进行查询 | present                     | 取值范围 [(present-179d), present] ，查询窗口最大为48小时，窗口平移范围为最近180天，已完全撤销的历史订单的查询窗口平移范围只有最近2小时(state="canceled") |
| start-date | false    | string | 查询开始日期, 日期格式yyyy-mm-dd。 以订单生成时间进行查询    | -1d 查询结束日期的前1天     | 取值范围 [((end-date) – 1), (end-date)] ，查询窗口最大为2天，窗口平移范围为最近180天，已完全撤销的历史订单的查询窗口平移范围只有最近2小时(state="canceled") |
| end-date   | false    | string | 查询结束日期, 日期格式yyyy-mm-dd。 以订单生成时间进行查询    | today                       | 取值范围 [(today-179), today] ，查询窗口最大为2天，窗口平移范围为最近180天，已完全撤销的历史订单的查询窗口平移范围只有最近2小时(state="canceled") |
| states     | true     | string | 查询的订单状态组合，使用','分割                              |                             | 所有可能的订单状态（见本章节简介）                           |
| from       | false    | string | 查询起始 ID                                                  |                             | 如果是向后查询，则赋值为上一次查询结果中得到的最后一条id ；如果是向前查询，则赋值为上一次查询结果中得到的第一条id |
| direct     | false    | string | 查询方向                                                     |                             | prev 向前；next 向后                                         |
| size       | false    | string | 查询记录大小                                                 | 100                         | [1, 100]                                                     |


> Response:

```json
{  
  "data": [
    {
      "id": 59378,
      "symbol": "ethusdt",
      "account-id": 100009,
      "amount": "10.1000000000",
      "price": "100.1000000000",
      "created-at": 1494901162595,
      "type": "buy-limit",
      "field-amount": "10.1000000000",
      "field-cash-amount": "1011.0100000000",
      "field-fees": "0.0202000000",
      "finished-at": 1494901400468,
      "user-id": 1000,
      "source": "api",
      "state": "filled",
      "canceled-at": 0
    }
    ...
  ]
}
```

### 响应数据

| 参数名称          | 是否必须 | 数据类型 | 描述                                                         | 取值范围                                                     |
| ----------------- | -------- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| account-id        | true     | long     | 账户 ID                                                      |                                                              |
| amount            | true     | string   | 订单数量                                                     |                                                              |
| canceled-at       | false    | long     | 接到撤单申请的时间                                           |                                                              |
| created-at        | true     | long     | 订单创建时间                                                 |                                                              |
| field-amount      | true     | string   | 已成交数量                                                   |                                                              |
| field-cash-amount | true     | string   | 已成交总金额                                                 |                                                              |
| field-fees        | true     | string   | 已成交手续费（准确数值请参考matchresults接口）                   |                                                              |
| finished-at       | false    | long     | 最后成交时间                                                 |                                                              |
| id                | true     | long     | 订单ID，无大小顺序，可作为下一次翻页查询请求的from字段       |                                                              |
| client-order-id   | false    | string   | 用户自编订单号（所有open订单可返回client-order-id（如有）；仅7天内（基于订单创建时间）的closed订单（state <> canceled）可返回client-order-id（如有）；仅8小时内（基于订单创建时间）的closed订单（state = canceled）可被查询） |                                                              |
| price   | true   | string   | 订单价格  |        |
| source  | true   | string   | 订单来源  | 所有可能的订单来源（见本章节简介） |
| state  | true   | string   | 订单状态  | 所有可能的订单状态（见本章节简介） |
| symbol  | true | string  | 交易对  | btcusdt, ethbtc, rcneth ...     |
| type   | true  | string  | 订单类型  | 所有可能的订单类型（见本章节简介） |
| stop-price  | false    | string   | 止盈止损订单触发价格  |   |
| operator  | false    | string   | 止盈止损订单触发价运算符  | gte,lte   |

### start-date, end-date相关错误码

| 错误码             | 对应错误场景                                                 |
| ------------------ | ------------------------------------------------------------ |
| invalid_interval   | start date小于end date; 或者 start date 与end date之间的时间间隔大于2天 |
| invalid_start_date | start date是一个180天之前的日期；或者start date是一个未来的日期 |
| invalid_end_date   | end date 是一个180天之前的日期；或者end date是一个未来的日期 |



## 当前和历史成交

API Key 权限：读取<br>
限频值（NEW）：20次/2s

此接口基于搜索条件查询当前和历史成交记录。

### HTTP 请求

- GET `/v1/order/matchresults`


### 请求参数

| 参数名称   | 是否必须 | 类型   | 描述                                                         | 默认值                      | 取值范围                                                     |
| ---------- | -------- | ------ | ------------------------------------------------------------ | --------------------------- | ------------------------------------------------------------ |
| symbol     | true     | string | 交易对                                                       | N/A                         | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`）       |
| types      | false    | string | 查询的订单类型组合，使用','分割                              | all                         | 所有可能的订单类型（见本章节简介）                           |
| start-time | false    | long   | 查询开始时间, 时间格式UTC time in millisecond。 以订单生成时间进行查询 | -48h 查询结束时间的前48小时 | 取值范围 [((end-time) – 48h), (end-time)] ，查询窗口最大为48小时，窗口平移范围为最近180天。 |
| end-time   | false    | long   | 查询结束时间, 时间格式UTC time in millisecond。 以订单生成时间进行查询 | present                     | 取值范围 [(present-179d), present] ，查询窗口最大为48小时，窗口平移范围为最近180天。 |
| start-date | false    | string | 查询开始日期（新加坡时区）日期格式yyyy-mm-dd                 | -1d 查询结束日期的前1天     | 取值范围 [((end-date) – 1), (end-date)] ，查询窗口最大为2天，窗口平移范围为最近61天。 |
| end-date   | false    | string | 查询结束日期（新加坡时区）, 日期格式yyyy-mm-dd               | today                       | 取值范围 [(today-60), today] ，查询窗口最大为2天，窗口平移范围为最近61天 |
| from       | false    | string | 查询起始 ID                                                  | N/A                         | 如果是向后查询，则赋值为上一次查询结果中得到的最后一条id（不是trade-id） ；如果是向前查询，则赋值为上一次查询结果中得到的第一条id（不是trade-id） |
| direct     | false    | string | 查询方向                                                     | next                        | prev 向前；next 向后                                         |
| size       | false    | string | 查询记录大小                                                 | 100                         | [1，500]                                                     |

> Response:

```json
{  
  "data": [
    {
      "id": 29553,
      "order-id": 59378,
      "match-id": 59335,
      "symbol": "ethusdt",
      "type": "buy-limit",
      "source": "api",
      "price": "100.1000000000",
      "filled-amount": "9.1155000000",
      "filled-fees": "0.0182310000",
      "created-at": 1494901400435,
      "trade-id": 100282808529,
      "role": taker,
      "filled-points": "0.0",
      “fee-deduct-state”:"done",
      "fee-deduct-currency": ""
    }
    ...
  ]
}
```

### 响应数据

<aside class="notice">返回的主数据对象为一个对象数组，其中每一个元件代表一个交易结果。</aside>

| 参数名称            | 是否必须 | 数据类型 | 描述                                                         | 取值范围                                                     |
| ------------------- | -------- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| created-at          | true     | long     | 该成交记录创建的时间戳（略晚于成交时间）                     |                                                              |
| filled-amount       | true     | string   | 成交数量                                                     |                                                              |
| filled-fees         | true     | string   | 交易手续费（正值）或交易返佣（负值）                         |                                                              |
| fee-currency        | true     | string   | 交易手续费或交易返佣币种（买单的交易手续费币种为基础币种，卖单的交易手续费币种为计价币种；买单的交易返佣币种为计价币种，卖单的交易返佣币种为基础币种） |                                                              |
| id                  | true     | long     | 订单成交记录 ID，无大小顺序，可作为下一次翻页查询请求的from字段 |                                                              |
| match-id            | true     | long     | 撮合 ID                                                      |                                                              |
| order-id            | true     | long     | 订单 ID                                                      |                                                              |
| trade-id            | false    | integer  | 唯一成交编号                                                 |                                                              |
| price               | true     | string   | 成交价格                                                     |                                                              |
| source              | true     | string   | 订单来源                                                     | api                                                          |
| symbol              | true     | string   | 交易对                                                       | btcusdt, ethbtc, rcneth ...                                  |
| type                | true     | string   | 订单类型                                                     | 所有可能的订单类型（见本章节简介） |
| role                | true     | string   | 成交角色                                                     | maker,taker                                                  |
| filled-points       | true     | string   | 抵扣数量（可为ht）                                  |                                                              |
| fee-deduct-currency | true     | string   | 抵扣类型                                                     | ht                                                  |
| fee-deduct-state    | true     | string   | 抵扣状态                                                     | 抵扣中：ongoing，抵扣完成：done                              |



### start-date, end-date相关错误码 

| 错误码             | 对应错误场景                                                 |
| ------------------ | ------------------------------------------------------------ |
| invalid_interval   | start date小于end date; 或者 start date 与end date之间的时间间隔大于2天 |
| invalid_start_date | start date是一个61天之前的日期；或者start date是一个未来的日期 |
| invalid_end_date   | end date 是一个61天之前的日期；或者end date是一个未来的日期  |


## 获取用户当前手续费率

Api用户查询交易对费率，一次限制最多查10个交易对，子用户的费率和母用户保持一致

API Key 权限：读取

```shell
curl "https://api.huobi.com.hk/v2/reference/transact-fee-rate?symbols=btcusdt,ethusdt,ltcusdt"
```

### HTTP 请求

- GET `/v2/reference/transact-fee-rate`

### 请求参数

| 参数    | 数据类型 | 是否必须 | 默认值 | 描述                     | 取值范围                                                |
| ------- | -------- | -------- | ------ | ------------------------ | ------------------------------------------------------- |
| symbols | string   | true     | NA     | 交易对，可多填，逗号分隔 | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`）> |

> Response:

```json
{
  "code": "200",
  "data": [
     {
        "symbol": "btcusdt",
        "makerFeeRate":"0.002",
        "takerFeeRate":"0.002",
        "actualMakerRate": "0.002",
        "actualTakerRate":"0.002
     },
     {
        "symbol": "ethusdt",
        "makerFeeRate":"0.002",
        "takerFeeRate":"0.002",
        "actualMakerRate": "0.002",
        "actualTakerRate":"0.002
    },
     {
        "symbol": "ltcusdt",
        "makerFeeRate":"0.002",
        "takerFeeRate":"0.002",
        "actualMakerRate": "0.002",
        "actualTakerRate":"0.002
    }
  ]
}
```
### 响应数据

|      | 字段名称          | 数据类型 | 描述                                                         |      |
| ---- | ----------------- | -------- | ------------------------------------------------------------ | ---- |
|      | code              | integer  | 状态码                                                       |      |
|      | message           | string   | 错误描述（如有）                                             |      |
|      | data              | object   |                                                              |      |
|      | { symbol          | string   | 交易代码                                                     |      |
|      | makerFeeRate      | string   | 基础费率 - 被动方，如适用交易手续费返佣，返回返佣费率（负值） |      |
|      | takerFeeRate      | string   | 基础费率 - 主动方                                            |      |
|      | actualMakerRate   | string   | 抵扣后费率 - 被动方，如不适用抵扣或未启用抵扣，返回基础费率；如适用交易手续费返佣，返回返佣费率（负值） |      |
|      | actualTakerRate } | string   | 抵扣后费率 – 主动方，如不适用抵扣或未启用抵扣，返回基础费率  |      |

注：<br>
- 如makerFeeRate/actualMakerRate为正值，该字段意为交易手续费率；<br>


## 常见错误码

以下是交易相关接口返回的返回码以及说明。

| 返回码                                                       | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| base-argument-unsupported                                    | 某参数不支持，请检查参数                                     |
| base-system-error                                            | 系统错误，如果是撤单：缓存中查不到订单状态，该订单无法撤单；如果是下单：订单入缓存失败，请再次尝试 |
| login-required                                               | url中没有Signature参数或找不到此用户（key与账户id不对应等情况） |
| parameter-required                                           | 止盈止损订单缺少参数 stop-price或operator                    |
| base-record-invalid                                          | 暂时未找到数据，请稍后重试                                   |
| order-amount-over-limit                                      | 订单数量超出限额                                             |
| base-symbol-trade-disabled                                   | 该交易对被禁止交易                                           |
| base-operation-forbidden                                     | 用户不在白名单内或该币种不允许OTC交易等禁止行为              |
| account-get-accounts-inexistent-error                        | 该账户在该用户下不存在                                       |
| account-account-id-inexistent                                | 该账户不存在                                                 |
| operation-forbidden-for-fl-account-state                     | 账户爆仓状态下禁止订单操作                                   |
| sub-user-auth-required                                       | 子用户未开通逐仓杠杆权限                                     |
| order-disabled                                               | 交易对暂停，无法下单                                         |
| cancel-disabled                                              | 交易对暂停，无法撤单                                         |
| order-invalid-price                                          | 下单价格非法（如市价单不能有价格，或限价单价格超过市场价10%） |
| order-accountbalance-error                                   | 账户余额不足                                                 |
| order-limitorder-price-min-error                             | 卖出价格不能低于指定价格                                     |
| order-limitorder-price-max-error                             | 买入价格不能高于指定价格                                     |
| order-limitorder-amount-min-error                            | 下单数量不能低于指定数量                                     |
| order-limitorder-amount-max-error                            | 下单数量不能高于指定数量                                     |
| order-etp-nav-price-min-error                                | 下单价格不能低于净值的指定比率                               |
| order-etp-nav-price-max-error                                | 下单价格不能高于净值等指定比率                               |
| order-orderprice-precision-error                             | 交易价格精度错误                                             |
| order-orderamount-precision-error                            | 交易数额精度错误                                             |
| order-value-min-error                                        | 订单交易额不能低于指定额度                                   |
| order-marketorder-amount-min-error                           | 卖出数量不能低于指定数量                                     |
| order-marketorder-amount-buy-max-error                       | 市价单买入额度不能高于指定额度                               |
| order-marketorder-amount-sell-max-error                      | 市价单卖出数量不能高于指定数量                               |
| order-holding-limit-failed                                   | 下单超出该币种的持仓限额                                     |
| order-type-invalid                                           | 订单类型非法                                                 |
| order-orderstate-error                                       | 订单状态错误                                                 |
| order-date-limit-error                                       | 查询时间不能超过系统限制                                     |
| order-source-invalid                                         | 订单来源非法                                                 |
| order-update-error                                           | 更新数据错误                                                 |
| order-fl-cancellation-is-disallowed                          | 禁止撤销爆仓单                                               |
| operation-forbidden-for-fl-account-state                     | 账户爆仓状态下禁止订单操作                                   |
| operation-forbidden-for-lock-account-state                   | 账户lock状态下禁止订单操作或c2c借款账户被禁止下撤单          |
| fl-order-already-existed                                     | 爆仓单已经存在 而且未成交                                    |
| order-user-cancel-forbidden                                  | 订单类型为IOC或FOK 不允许撤单                                |
| account-state-invalid                                        | 不支持的爆仓账户状态                                         |
| order-price-greater-than-limit                               | 下单价格高于开盘前下单限制价格，请重新下单                   |
| order-price-less-than-limit                                  | 下单价格低于开盘前下单限制价格，请重新下单                   |
| order-stop-order-hit-trigger                                 | 止盈止损单下单被当前价触发                                   |
| market-orders-not-support-during-limit-price-trading         | 限时下单不支持市价单                                         |
| price-exceeds-the-protective-price-during-limit-price-trading | 限价时间内价格超出保护价                                     |
| invalid-client-order-id                                      | client order id 在最近的下单或撤单参数中已被使用             |
| invalid-interval                                             | 查询起止窗口设置错误                                         |
| invalid-start-date                                           | 查询起始日期含非法取值                                       |
| invalid-end-date                                             | 查询起始日期含非法取值                                       |
| invalid-start-time                                           | 查询起始时间含非法取值                                       |
| invalid-end-time                                             | 查询起始时间含非法取值                                       |
| validation-constraints-required                              | 指定的必填参数缺失                                           |
| symbol-not-support                                           | 交易对不支持，全仓杠杆或c2c                                  |
| not-found                                                    | 撤单时订单不存在                                             |
| base-not-found                                               | 未找到记录                                                   |
| base_record_invalid                                          | 订单不存在 （传错订单ID）                                    |
| dw_cancel_withdraw_failed                                    | 取消失败，订单状态错误                                       |
| base_update_error                                            | 内部系统错误                                                 |

## 常见问题

### Q1：client-order-id是什么？
A： client-order-id作为下单请求标识的一个参数，类型为字符串，长度为64。 此id为用户自己生成，8小时内有效。如果是终态订单仅2小时有效。

### Q2：如何获取下单数量、金额、小数限制、精度的信息？
A： 可使用 Rest API `GET /v1/common/symbols` 获取相关币对信息， 下单时注意最小下单数量和最小下单金额的区别。 

常见返回错误如下：  

- order-value-min-error: 下单金额小于最小交易额  
- order-orderprice-precision-error : 限价单价格精度错误  
- order-orderamount-precision-error : 下单数量精度错误  
- order-limitorder-price-max-error : 限价单价格高于限价阈值  
- order-limitorder-price-min-error : 限价单价格低于限价阈值  
- order-limitorder-amount-max-error : 限价单数量高于限价阈值  
- order-limitorder-amount-min-error : 限价单数量低于限价阈值  

### Q3： 为什么收到订单成功成交的消息后再次进行下单，返回余额不足？
A：为保证订单的及时送达以及低延时， 订单推送的结果是在撮合后直接推送，此时订单可能并未完成资产的清算。  

建议使用以下方式保证资金可以正确下单：

1. 结合资产推送主题`accounts`同步接收资产变更的消息，确保资金已经完成清算。

2. 收到订单推送消息时，使用Rest接口调用账户余额，验证账户资金是否足够。

3. 账户中保留相对充足的资金余额。

### Q4: 成交明细里的filled-fees和filled-points有什么区别？
A: 成交中的成交手续费分为普通手续费以及抵扣手续费两种类型，两种类型不会同时存在。

1. 普通手续费表示，在成交时，未开启HT抵扣，使用原币进行手续费扣除。例如：在BTCUSDT币种对下购买BTC，filled-fees字段不为空，表示扣除了普通手续费，单位是BTC。

2. 抵扣手续费表示，在成交时，开启了HT抵扣，使用HT进行手续费的抵扣。例如BTCUSDT币种对下购买BTC，HT充足时，filled-fees为空，filled-points不为空，表示扣除了HT作为手续费，扣除单位需参考fee-deduct-currency字段

### Q5: 成交明细中match-id和trade-id有什么区别？
A: match-id表示订单在撮合中的顺序号，trade-id表示成交时的序号， 一个match-id可能有多个trade-id（成交时），也可能没有trade-id(创建订单、撤销订单)

### Q6: 为什么基于当前盘口买一或者卖一价格进行下单触发了下单限价错误？
A: 当前火币有基于最新成交价上下一定幅度的限价保护，对流动性不好的币，基于盘口数据下单可能会触发限价保护。建议基于ws推送的成交价+盘口数据信息进行下单


# 策略委托

## 简介

策略委托，目前仅包括计划委托及追踪委托。与现有止盈止损订单相比，计划委托/追踪委托有以下显著不同 –<br>特别说明，如遇行情或其他极端情况，策略委托单可能会延迟触发。

1）	计划委托/追踪委托被创建后，未触发前，交易所将不会冻结委托保证金。仅当计划委托成功触发后，交易所才会冻结该委托的保证金。<br>
2）	计划委托支持限价单和市价单类型，追踪委托仅支持市价单。<br>
3）	追踪委托是一种更高级的计划委托。通常的计划委托仅可设置触发价一个条件，当市场最新价格达到触发价时，该订单即被送入撮合。追踪委托在计划委托的基础上增加了一个设定条件，即回调幅度。当市场最新价达到设定的触发价时，该追踪委托并不会被送入撮合，而是继续等待市场价格回转出现。当市场价格回转幅度达到设定的回调幅度时，该追踪委托才会被送入撮合。火币香港支持用户设定的回调幅度范围为0.1%~5%。<br>

<aside class="notice">访问策略委托相关的接口需要进行签名认证。</aside>
<aside class="notice"> 在计划委托/追踪委托上线一段时间后，火币香港可能会下线现有止盈止损订单类型。届时将另行通知。 </aside>

## 策略委托下单

POST /v2/algo-orders<br>
API Key 权限：交易<br>
限频值（NEW）：20次/2秒<br>
仅可通过此节点下单策略委托，不可通过现货/杠杆交易相关接口下单策略委托<br>

### 请求参数
|	名称	|	类型	|	是否必需	|	默认值|	描述	|	取值范围	|
|	-----	|	-----	|	------	|	----	|	------	|	----	|
|	accountId	|	integer	|	TRUE	|		|	账户编号	|当前支持spot账户ID		|
|	symbol	|	string	|	TRUE	|		|	交易代码	|		|
|	orderPrice	|	string	|	FALSE	|		|	订单价格（对市价单无效）	|		|
|	orderSide	|	string	|	TRUE	|		|	订单方向	|	buy,sell	|
|	orderSize	|	string	|	FALSE	|		|	订单数量（对市价买单无效）	|		|
|	orderValue	|	string	|	FALSE	|		|	订单金额（仅对市价买单有效）	|		|
|	timeInForce	|	string	|	FALSE	|	gtc for orderType=limit; ioc for orderType=market	|	订单有效期	|	gtc(对orderType=market无效),boc(对orderType=market无效),ioc,fok(对orderType=market无效)	|
|	orderType	|	string	|	TRUE	|		|	订单类型	|	limit,market	|
|	clientOrderId	|	string	|	TRUE	|		|	用户自编订单号（最长64位）	|		|
|	stopPrice	|	string	|	TRUE	|		|	触发价	|		|
|	trailingRate	|	string	|	FALSE	|		|	 回调幅度（仅对追踪委托有效）	|	[0.001,0.050]	|

注：<br>
•	orderPrice与stopPrice的偏离率不能超出交易所对该币对的价格限制（百分比），例如，当交易所限定，限价买单的订单价格不能高于市价的110%时，该限制比率也同样适用于orderPrice与stopPrice之比。<br>
•	用户须保证策略委托在触发时，其clientOrderId不与该用户的其它（8小时内）订单重复，否则，会导致触发失败。<br>
•	用户须保证相关账户（accountId）中存有足够资金作为委托保证金，否则将导致策略委托触发时校验失败。<br>
•	timeInForce字段中各枚举值含义：gtc - good till cancel (除非用户主动撤销否则一直有效)，boc - book or cancel（即post only，或称book only，除非成功挂单否则自动撤销），ioc - immediate or cancel（立即成交剩余部分自动撤销），fok - fill or kill（立即全部成交否则全部自动撤销）<br>

> Response

```json
{
    "code": 200,
    "data": {
        "clientOrderId": "a001"
    }
}
```

### 响应数据
|	名称	|	类型	|	是否必需	|	描述	|
|	-----	|	-----	|	------	|	----	|
|	code	|	integer	|	TRUE	|状态码	|
|	message	|	string	|	FALSE	|错误描述（如有）	|
|	data	|	object	|	TRUE	|	|
|	{ clientOrderId }	|	string	|	TRUE	|用户自编订单号	|

## 策略委托（触发前）撤单

POST /v2/algo-orders/cancellation<br>
API Key 权限：交易<br>
限频值（NEW）：20次/2秒<br>
单次请求最多批量撤销50张订单<br>
如需撤销已成功触发的订单，须通过现货/杠杆交易相关接口完成<br>
如需撤销未触发的订单，仅可通过此节点撤销，不可通过现货/杠杆交易相关接口撤销<br>

### 请求参数
|	名称	|	类型	|	是否必需	|	默认值|	描述	|	取值范围	|
|	-----	|	-----	|	------	|	----	|	------	|	----	|
|	clientOrderIds	|	string[]	|	TRUE	|		|	用户自编订单号（可多填，以逗号分隔）	|		|

> Response

```json
{
    "code": 200,
    "data": {
        "accepted": [
            "a001"
        ],
        "rejected": []
    }
}
```

### 响应数据
|	名称	|	类型	|	是否必需	|	描述	|
|	-----	|	-----	|	------	|	----	|
|	code	|	integer	|	TRUE	|状态码	|
|	message	|	string	|	FALSE	|错误描述（如有）	|
|	data	|	object	|	TRUE	|按用户请求顺序排列	|
|	{ accepted	|	string[]	|	FALSE	|已接受订单clientOrderId列表	|
|	rejected }	|	string[]	|	TRUE	|已拒绝订单clientOrderId列表	|

## 查询未触发OPEN策略委托

GET /v2/algo-orders/opening<br>
API Key 权限：读取<br>
限频值（NEW）：20次/2秒<br>
以orderOrigTime检索<br>
未触发OPEN订单，指的是已成功下单，但尚未触发，订单状态orderStatus为created的订单<br>
未触发OPEN订单，可通过此节点查询，但不可通过现货/杠杆交易相关接口查询<br>

### 请求参数
|	名称	|	类型	|	是否必需	|	默认值|	描述	|	取值范围	|
|	-----	|	-----	|	------	|	----	|	------	|	----	|
|	accountId	|	integer	|	FALSE	|	all	|	账户编号	|		|
|	symbol	|	string	|	FALSE	|	all	|	交易代码	|		|
|	orderSide	|	string	|	FALSE	|	all	|	订单方向	|	buy,sell	|
|	orderType	|	string	|	FALSE	|	all	|	订单类型	|	limit,market	|
|	sort	|	string	|	FALSE	|	desc	|	检索方向	|asc - 由远及近, desc - 由近及远		|
|	limit	|	integer	|	FALSE	|	100	|	单页最大返回条目数量	|[1,500]		|
|	fromId	|	long	|	FALSE	|		|	起始编号（仅在下页查询时有效）	|		|

> Response

```json
{
    "code": 200,
    "data": [
        {
            "lastActTime": 1593235832976,
            "orderOrigTime": 1593235832937,
            "symbol": "btcusdt",
            "orderSize": "0.001",
            "stopPrice": "5001",
            "accountId": 5260185,
            "source": "api",
            "clientOrderId": "a001",
            "orderSide": "buy",
            "orderType": "limit",
            "timeInForce": "gtc",
            "orderPrice": "5000",
            "orderStatus": "created"
        }
    ]
}
```

### 响应数据
|	名称	|	类型	|	是否必需	|	描述	|
|	-----	|	-----	|	------	|	----	|
|	code	|	integer	|	TRUE	|状态码	|
|	message	|	string	|	FALSE	|错误描述（如有）	|
|	data	|	object	|	TRUE	|按用户请求参数sort指定顺序排列	|
|	{ accountId	|	integer	|	TRUE	|账户编号	|
|	source	|	string	|	TRUE	|订单来源（api,web,ios,android,mac,windows,sys）	|
|	clientOrderId	|	string	|	TRUE	|用户自编订单号	|
|	symbol	|	string	|	TRUE	|交易代码	|
|	orderPrice	|	string	|	TRUE	|订单价格（对市价单无效）	|
|	orderSize	|	string	|	FALSE	|订单数量（对市价买单无效）	|
|	orderValue	|	string	|	FALSE	|订单金额（仅对市价买单有效）	|
|	orderSide	|	string	|	TRUE	|订单方向	|
|	timeInForce	|	string	|	TRUE	|订单有效期|
|	orderType	|	string	|	TRUE	|订单类型	|
|	stopPrice	|	string	|	TRUE	|触发价	|
|	trailingRate	|	string	|	FALSE	|	回调幅度（仅对追踪委托有效）	|
|	orderOrigTime	|	long	|	TRUE	|订单创建时间	|
|	lastActTime	|	long	|	TRUE	|订单最近更新时间	|
|	orderStatus }	|	string	|	TRUE	|订单状态（created）	|
|	nextId	|	long	|	FALSE	|下页起始编号（仅在查询结果需要分页返回时传此字段）	|

## 查询策略委托历史

GET /v2/algo-orders/history<br>
API Key 权限：读取<br>
限频值（NEW）：20次/2秒<br>
以orderOrigTime检索<br>
历史终态订单包括，触发前被主动撤销的策略委托（orderStatus=canceled），触发失败的策略委托（orderStatus=rejected），触发成功的策略委托（orderStatus=triggered）。<br>
如需查询已成功触发订单的后续状态，须通过现货/杠杆交易相关接口完成<br>
触发前被主动撤销的策略委托及触发失败的策略委托，可通过此节点查询，但不可通过现货/杠杆交易相关接口查询<br>

### 请求参数
|	名称	|	类型	|	是否必需	|	默认值|	描述	|	取值范围	|
|	-----	|	-----	|	------	|	----	|	------	|	----	|
|	accountId	|	integer	|	FALSE	|	all	|	账户编号	|		|
|	symbol	|	string	|	TRUE	|		|	交易代码	|		|
|	orderSide	|	string	|	FALSE	|	all	|	订单方向	|	buy,sell	|
|	orderType	|	string	|	FALSE	|	all	|	订单类型	|	limit,market	|
|	orderStatus	|	string	|	TRUE	|		|	订单状态	|	canceled,rejected,triggered	|
|	startTime	|	long	|	FALSE	|		|	远点时间	||
|	endTime	|	long	|	FALSE	|当前时间		|	近点时间 | |
|	sort	|	string	|	FALSE	|	desc	|	检索方向	|asc - 由远及近, desc - 由近及远		|
|	limit	|	integer	|	FALSE	|	100	|	单页最大返回条目数量	|[1,500]		|
|	fromId	|	long	|	FALSE	|		|	起始编号（仅在下页查询时有效）	|		|

> Response

```json
{
    "code": 200,
    "data": [
        {
            "orderOrigTime": 1593235832937,
            "lastActTime": 1593236344401,
            "symbol": "btcusdt",
            "source": "api",
            "orderSide": "buy",
            "orderType": "limit",
            "timeInForce": "gtc",
            "clientOrderId": "a001",
            "accountId": 5260185,
            "orderPrice": "5000",
            "orderSize": "0.001",
            "stopPrice": "5001",
            "orderStatus": "canceled"
        }
    ]
}
```

### 响应数据
|	名称	|	类型	|	是否必需	|	描述	|
|	-----	|	-----	|	------	|	----	|
|	code	|	integer	|	TRUE	|状态码	|
|	message	|	string	|	FALSE	|错误描述（如有）	|
|	data	|	object	|	TRUE	|按用户请求参数sort指定顺序排列	|
|	{ accountId	|	integer	|	TRUE	|账户编号	|
|	source	|	string	|	TRUE	|订单来源	|
|	clientOrderId	|	string	|	TRUE	|用户自编订单号	|
|	orderId	|	string	|	FALSE	|订单编号（仅对orderStatus=triggered有效）	|
|	symbol	|	string	|	TRUE	|交易代码	|
|	orderPrice	|	string	|	TRUE	|订单价格（对市价单无效）	|
|	orderSize	|	string	|	FALSE	|订单数量（对市价买单无效）	|
|	orderValue	|	string	|	FALSE	|订单金额（仅对市价买单有效）	|
|	orderSide	|	string	|	TRUE	|订单方向	|
|	timeInForce	|	string	|	TRUE	|订单有效期|
|	orderType	|	string	|	TRUE	|订单类型	|
|	stopPrice	|	string	|	TRUE	|触发价	|
|	trailingRate	|	string	|	FALSE	|	回调幅度（仅对追踪委托有效）	|
|	orderOrigTime	|	long	|	TRUE	|订单创建时间	|
|	lastActTime	|	long	|	TRUE	|订单最近更新时间	|
|	orderCreateTime	|	long	|	FALSE	|订单触发时间（仅对orderStatus=triggered有效）	|
|	orderStatus	|	string	|	TRUE	|订单状态（triggered,canceled,rejected）	|
|	errCode	|	integer	|	FALSE	|订单被拒状态码（仅对orderStatus=rejected有效）	|
|	errMessage }	|	string	|	FALSE	|订单被拒错误消息（仅对orderStatus=rejected有效）	|
|	nextId	|	long	|	FALSE	|下页起始编号（仅在查询结果需要分页返回时传此字段）	|

## 查询特定策略委托

GET /v2/algo-orders/specific<br>
API Key 权限：读取<br>
限频值（NEW）：20次/2秒<br>
以orderOrigTime检索<br>
如需查询已成功触发订单的后续状态，须通过现货/杠杆交易相关接口完成<br>
未触发的策略委托及触发失败的策略委托，可通过此节点查询，但不可通过现货/杠杆交易相关接口查询<br>

### 请求参数
|	名称	|	类型	|	是否必需	|	默认值|	描述	|	取值范围	|
|	-----	|	-----	|	------	|	----	|	------	|	----	|
|	clientOrderId	|	string	|	TRUE	|		| 用户自编订单号|		|

> Response

```json
{
    "code": 200,
    "data": {
        "lastActTime": 1593236344401,
        "orderOrigTime": 1593235832937,
        "symbol": "btcusdt",
        "orderSize": "0.001",
        "stopPrice": "5001",
        "accountId": 5260185,
        "source": "api",
        "clientOrderId": "a001",
        "orderSide": "buy",
        "orderType": "limit",
        "timeInForce": "gtc",
        "orderPrice": "5000",
        "orderStatus": "canceled"
    }
}
```

### 响应数据
|	名称	|	类型	|	是否必需	|	描述	|
|	-----	|	-----	|	------	|	----	|
|	code	|	integer	|	TRUE	|状态码	|
|	message	|	string	|	FALSE	|错误描述（如有）	|
|	data	|	object	|	TRUE	|	|
|	{ accountId	|	integer	|	TRUE	|账户编号	|
|	source	|	string	|	TRUE	|订单来源	|
|	clientOrderId	|	string	|	TRUE	|用户自编订单号	|
|	orderId	|	string	|	FALSE	|订单编号（仅对orderStatus=triggered有效）	|
|	symbol	|	string	|	TRUE	|交易代码	|
|	orderPrice	|	string	|	TRUE	|订单价格（对市价单无效）	|
|	orderSize	|	string	|	FALSE	|订单数量（对市价买单无效）	|
|	orderValue	|	string	|	FALSE	|订单金额（仅对市价买单有效）	|
|	orderSide	|	string	|	TRUE	|订单方向	|
|	timeInForce	|	string	|	TRUE	|订单有效期|
|	orderType	|	string	|	TRUE	|订单类型	|
|	stopPrice	|	string	|	TRUE	|触发价	|
|	trailingRate	|	string	|	FALSE	|	回调幅度（仅对追踪委托有效）	|
|	orderOrigTime	|	long	|	TRUE	|订单创建时间	|
|	lastActTime	|	long	|	TRUE	|订单最近更新时间	|
|	orderCreateTime	|	long	|	FALSE	|订单触发时间（仅对orderStatus=triggered有效）	|
|	orderStatus	|	string	|	TRUE	|订单状态（created,triggered,canceled,rejected）	|
|	errCode	|	integer	|	FALSE	|订单被拒状态码（仅对orderStatus=rejected有效）	|
|	errMessage }	|	string	|	FALSE	|订单被拒错误消息（仅对orderStatus=rejected有效）	|

## 常见错误码

以下是策略委托接口返回的错误码和说明。

| 错误码 | 说明                       |
| ------ | -------------------------- |
| 2002   | 参数无效                   |
| 1001   | 无效的url                  |
| 1002   | 请重新登录后重试           |
| 1003   | API签名无效                |
| 1005   | 访问权重不足               |
| 1006   | 超出访问限制               |
| 1007   | 未查询到记录               |
| 2003   | 交易功能暂停               |
| 3002   | 订单数量精度错误           |
| 3003   | 触发价格精度错误           |
| 3004   | 低于限价单最小订单数量限制 |
| 3005   | 超出限价单最大订单数量限制 |
| 3006   | 超出限价单最高订单价格限制 |
| 3007   | 低于限价单最低订单价格限制 |
| 3008   | 低于最小订单金额限制       |
| 3009   | 低于市价单最小订单数量限制 |
| 3010   | 超出市价单最大订单数量限制 |
| 3100   | 限价交易期间不接受市价申报 |



# Websocket行情数据

## 简介

### 接入URL

**香港站行情请求地址（除MBP增量推送及MBP全量REQ以外Websocket行情频道）**

**`wss://api.huobi.com.hk/ws`**  



**MBP增量推送及MBP全量REQ请求地址**

**`wss://api.huobi.com.hk/feed`**  



### 数据压缩

WebSocket 行情接口返回的所有数据都进行了 GZIP 压缩，需要 client 在收到数据之后解压。

### 心跳消息

```json
{"ping": 1492420473027} 
```

当用户的Websocket客户端连接到火币Websocket服务器后，服务器会定期（当前设为5秒）向其发送`ping`消息并包含一整数值。

```json
{"pong": 1492420473027} 
```

当用户的Websocket客户端接收到此心跳消息后，应返回`pong`消息并包含同一整数值。

<aside class="warning">当Websocket服务器连续两次发送了`ping`消息却没有收到任何一次`pong`消息返回后，服务器将主动断开与此客户端的连接。</aside>

### 订阅主题

> Sub request:

```json
{
  "sub": "market.btcusdt.kline.1min",
  "id": "id1"
}
```

成功建立与Websocket服务器的连接后，Websocket客户端发送请求以订阅特定主题：

{
  "sub": "topic to sub",
  "id": "id generate by client"
}

> Sub response:

```json
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.kline.1min",
  "ts": 1489474081631
}
```

成功订阅后，Websocket客户端将收到确认。

之后, 一旦所订阅的主题有更新，Websocket客户端将收到服务器推送的更新消息（push）。

### 取消订阅

> UnSub request:

```json
{
  "unsub": "market.btcusdt.trade.detail",
  "id": "id4"
}
```

取消订阅的格式如下：

{
  "unsub": "topic to unsub",
  "id": "id generate by client"
}

> UnSub response:

```json
{
  "id": "id4",
  "status": "ok",
  "unsubbed": "market.btcusdt.trade.detail",
  "ts": 1494326028889
}
```

取消订阅成功确认。

### 请求数据

Websocket服务器同时支持一次性请求数据（pull）。

一次性请求的格式如下：

{
  "req": "topic to req",
  "id": "id generate by client"
}

一次性返回数据的具体格式参见各个主题。

### 限频

数据请求（req）限频规则

单个连接每两次请求不能小于100ms。

## K线数据

### 主题订阅

一旦K线数据产生，Websocket服务器将通过此订阅主题接口推送至客户端：

`market.$symbol$.kline.$period$`

> 订阅请求

```json
{
  "sub": "market.ethbtc.kline.1min",
  "id": "id1"
}
```

### 参数

| 参数   | 数据类型 | 是否必需 | 描述     | 取值范围                                                     |
| ------ | -------- | -------- | -------- | ------------------------------------------------------------ |
| symbol | string   | true     | 交易代码 | btcusdt, ethbtc...等 |
| period | string   | true     | K线周期  | 1min, 5min, 15min, 30min, 60min, 4hour, 1day, 1mon, 1week, 1year |

> Response

```json
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.ethbtc.kline.1min",
  "ts": 1489474081631 //system response time
}
```

> Update example

```json
{
  "ch": "market.ethbtc.kline.1min",
  "ts": 1489474082831, //system update time
  "tick": {
    "id": 1489464480,
    "amount": 0.0,
    "count": 0,
    "open": 7962.62,
    "close": 7962.62,
    "low": 7962.62,
    "high": 7962.62,
    "vol": 0.0
  }
}
```

### 数据更新字段列表

| 字段   | 数据类型 | 描述                                        |
| ------ | -------- | ------------------------------------------- |
| id     | integer  | unix时间，同时作为K线ID                     |
| amount | float    | 成交量                                      |
| count  | integer  | 成交笔数                                    |
| open   | float    | 开盘价                                      |
| close  | float    | 收盘价（当K线为最晚的一根时，是最新成交价） |
| low    | float    | 最低价                                      |
| high   | float    | 最高价                                      |
| vol    | float    | 成交额, 即 sum(每一笔成交价 * 该笔的成交量) |


### 数据请求

用请求方式一次性获取K线数据需要额外提供以下参数：
（每次最多返回300条）

```json
{
  "req": "market.$symbol.kline.$period",
  "id": "id generated by client",
  "from": "from time in epoch seconds",
  "to": "to time in epoch seconds"
}
```

| 参数 | 数据类型 | 是否必需 | 缺省值                                | 描述                            | 取值范围                                                     |
| ---- | -------- | -------- | ------------------------------------- | ------------------------------- | ------------------------------------------------------------ |
| from | integer  | false    | 1501174800(2017-07-28T00:00:00+08:00) | 起始时间 (epoch time in second) | [1501174800, 2556115200]                                     |
| to   | integer  | false    | 2556115200(2050-01-01T00:00:00+08:00) | 结束时间 (epoch time in second) | [1501174800, 2556115200] or ($from, 2556115200] if "from" is set |



## 聚合行情(Ticker)数据

### 主题订阅

获取市场聚合行情数据，每100ms推送一次。

`market.$symbol.ticker`

> 订阅请求

```json
{
  "sub": "market.ethbtc.ticker",
  "id": "id1"
}
```

### 请求参数

| 参数   | 数据类型 | 是否必须 | 默认值 | 描述   | 取值范围                                               |
| ------ | -------- | -------- | ------ | ------ | ------------------------------------------------------ |
| symbol | string   | true     | NA     | 交易对 | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |

> Response:

```json
{
  "id":1499225271,
  "ts":1499225271000,
  "close":1885.0000,
  "open":1960.0000,
  "high":1985.0000,
  "low":1856.0000,
  "amount":81486.2926,
  "count":42122,
  "vol":157052744.85708200,
  "ask":[1885.0000,21.8804],
  "bid":[1884.0000,1.6702]
}
```

### 响应数据

| 字段名称 | 数据类型 | 描述                                     |
| -------- | -------- | ---------------------------------------- |
| id       | long     | NA                                       |
| amount   | float    | 以基础币种计量的交易量（以滚动24小时计） |
| count    | integer  | 交易次数（以滚动24小时计）               |
| open     | float    | 本阶段开盘价（以滚动24小时计）           |
| close    | float    | 本阶段最新价（以滚动24小时计）           |
| low      | float    | 本阶段最低价（以滚动24小时计）           |
| high     | float    | 本阶段最高价（以滚动24小时计）           |
| vol      | float    | 以报价币种计量的交易量（以滚动24小时计） |
| bid      | object   | 当前的最高买价 [price, size]             |
| ask      | object   | 当前的最低卖价 [price, size]             |

## 

## 市场深度行情数据

此主题发送最新市场深度快照。快照频率为每秒1次。

### 主题订阅

`market.$symbol.depth.$type`

> Subscribe request

```json
{
  "sub": "market.btcusdt.depth.step0",
  "id": "id1"
}
```

### 参数

| 参数   | 数据类型 | 是否必需 | 缺省值 | 描述         | 取值范围                                               |
| ------ | -------- | -------- | ------ | ------------ | ------------------------------------------------------ |
| symbol | string   | true     | NA     | 交易代码     | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |
| type   | string   | true     | step0  | 合并深度类型 | step0, step1, step2, step3, step4, step5               |

**"type" 合并深度类型**

| Value | Description                          |
| ----- | ------------------------------------ |
| step0 | 不合并深度                           |
| step1 | Aggregation level = precision*10     |
| step2 | Aggregation level = precision*100    |
| step3 | Aggregation level = precision*1000   |
| step4 | Aggregation level = precision*10000  |
| step5 | Aggregation level = precision*100000 |

当type值为‘step0’时，默认深度为150档;
当type值为‘step1’,‘step2’,‘step3’,‘step4’,‘step5’时，默认深度为20档。

> Response

```json
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.depth.step0",
  "ts": 1489474081631 //system response time
}
```

> Update example

```json
{
  "ch": "market.htusdt.depth.step0",
  "ts": 1572362902027, //system update time
  "tick": {
    "bids": [
      [3.7721, 344.86],// [price, size]
      [3.7709, 46.66] 
    ],
    "asks": [
      [3.7745, 15.44],
      [3.7746, 70.52]
    ],
    "version": 100434317651,
    "ts": 1572362902012 //quote time
  }
}
```

### 数据更新字段列表

<aside class="notice">在'tick'object下方呈现买盘卖盘深度列表</aside>

| 字段    | 数据类型 | 描述                         |
| ------- | -------- | ---------------------------- |
| bids    | object   | 当前的所有买单 [price, size] |
| asks    | object   | 当前的所有卖单 [price, size] |
| version | integer  | 内部字段                     |
| ts      | integer  | 新加坡时间的时间戳，单位毫秒 |

<aside class="notice">当symbol被设为"hb10"时，amount, count, vol均为零值 </aside>
### 数据请求

支持数据请求方式一次性获取市场深度数据：

```json
{
  "req": "market.btcusdt.depth.step0",
  "id": "id10"
}
```

## 市场深度MBP行情数据（增量推送）

用户可订阅此频道以接收最新深度行情Market By Price (MBP) 的增量数据推送；同时，该频道支持用户以req方式请求获取全量数据。

**MBP增量推送及MBP全量REQ请求地址**

**`wss://api.huobi.com.hk/feed`**  



建议下游数据处理方式：<br>
1）	订阅增量数据并开始缓存；<br>
2）	请求全量数据（同等档位数）并根据该全量消息的seqNum与缓存增量数据中的prevSeqNum对齐；<br>
3）	开始连续增量数据接收与计算，构建并持续更新MBP订单簿；<br>
4）	每条增量数据的prevSeqNum须与前一条增量数据的seqNum一致，否则意味着存在增量数据丢失，须重新获取全量数据并对齐；<br>
5）	如果收到增量数据包含新增price档位，须将该price档位插入MBP订单簿中适当位置；<br>
6）	如果收到增量数据包含已有price档位，但size不同，须替换MBP订单簿中该price档位的size；<br>
7）	如果收到增量数据某price档位的size为0值，须将该price档位从MBP订单簿中删除；<br>
8）	如果收到单条增量数据中包含两个及以上price档位的更新，这些price档位须在MBP订单簿中被同时更新。<br>

当前仅支持5档/20档MBP逐笔增量以及150档MBP快照增量的推送，二者的区别为 -<br>
1） 深度不同；<br>
2） 5档/20档为逐笔增量MBP行情，150档为100毫秒定时快照增量MBP行情；<br>
3） 当5档/20档订单簿仅发生单边行情变化时，增量推送仅包含单边行情更新，比如，推送消息中包含数组asks，但不含数组bids；<br>

```json
{
    "ch": "market.btcusdt.mbp.5",
    "ts": 1573199608679,
    "tick": {
        "seqNum": 100020146795,
        "prevSeqNum": 100020146794,
        "asks": [
            [645.140000000000000000, 26.755973959140651643]
        ]
    }
}
```
当150档订单簿仅发生单边行情变化时，增量推送包含双边行情更新，但其中一边行情为空，比如，推送消息中包含数组asks更新的同时，也包含bids空数组；<br>

```json
{
    "ch":"market.btcusdt.mbp.150",
    "ts":1573199608679,
    "tick":{
        "seqNum":100020146795,
        "prevSeqNum":100020146794,
        "bids":[ ],
        "asks":[
            [645.14,26.75597395914065]
        ]
    }
}
```
未来，150档增量推送的数据行为将与5档/20档增量保持一致，即，单边深度行情变更时，推送消息中将不包含另一边行情深度行情；<br>
4） 当150档订单簿在100毫秒时间间隔内未发生变化时，增量推送包含bids和asks空数组；<br>

```json
{
    "ch":"market.zecusdt.mbp.150",
    "ts":1585074391470,
    "tick":{
        "seqNum":100772868478,
        "prevSeqNum":100772868476,
        "bids":[  ],
        "asks":[  ]
    }
}
```
而5档/20档MBP逐笔增量，在订单簿未发生变化时，不推送数据；<br>
未来，150档增量推送的数据行为将与5档增量保持一致，即，在订单簿未发生变化时，不再推送空消息；<br>


REQ频道支持5档/20档/150档全量数据的获取。<br>

### 订阅增量推送

`market.$symbol.mbp.$levels`

> Sub request

```json
{
  "sub": "market.btcusdt.mbp.5",
  "id": "id1"
}
```

### 请求全量数据

`market.$symbol.mbp.$levels`

> Req request

```json
{
  "req": "market.btcusdt.mbp.5",
  "id": "id2"
}
```

### 参数

| 参数   | 数据类型 | 是否必需 | 缺省值 | 描述                         | 取值范围                         |
| ------ | -------- | -------- | ------ | ---------------------------- | -------------------------------- |
| symbol | string   | true     | NA     | 交易代码（不支持通配符）     |                                  |
| levels | integer  | true     | NA     | 深度档位（取值：5， 20， 150，400） | 当前仅支持5档，20档，150或400档深度 |

> Response (增量订阅)

```json
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.mbp.5",
  "ts": 1489474081631 //system response time
}
```

> Incremental Update (增量订阅)

```json
{
	"ch": "market.btcusdt.mbp.5",
	"ts": 1573199608679, //system update time
  "tick": {
           "seqNum": 100020146795,
            "prevSeqNum": 100020146794,
           "asks": [
                 [645.140000000000000000, 26.755973959140651643] // [price, size]
           ]
      }
}
```

> Response (全量请求)

```json
{
	"id": "id2",
	"rep": "market.btcusdt.mbp.150",
	"status": "ok",
	"data": {
		"seqNum": 100020142010,
		"bids": [
			[618.37, 71.594], // [price, size]
			[423.33, 77.726],
			[223.18, 47.997],
			[219.34, 24.82],
			[210.34, 94.463]
    ],
		"asks": [
			[650.59, 14.909733438479636],
			[650.63, 97.996],
			[650.77, 97.465],
			[651.23, 83.973],
			[651.42, 34.465]
		]
	}
}
```

### 数据更新字段列表

| 字段       | 数据类型 | 描述                                    |
| ---------- | -------- | --------------------------------------- |
| seqNum     | integer  | 消息序列号                              |
| prevSeqNum | integer  | 上一消息序列号                          |
| bids       | object   | 买盘，按price降序排列，["price","size"] |
| asks       | object   | 卖盘，按price升序排列，["price","size"] |

## 市场深度MBP行情数据（全量推送）

用户可订阅此频道以接收最新深度行情Market By Price (MBP) 的全量数据推送。推送频率为大约100毫秒一次。

### 订阅增量推送

`market.$symbol.mbp.refresh.$levels`

> Sub request

```json
{
"sub": "market.btcusdt.mbp.refresh.20",
"id": "id1"
}
```

### 参数

| 参数   | 数据类型 | 是否必需 | 缺省值 | 描述                     | 取值范围 |
| ------ | -------- | -------- | ------ | ------------------------ | -------- |
| symbol | string   | true     | NA     | 交易代码（不支持通配符） |          |
| levels | integer  | true     | NA     | 深度档位                 | 5,10,20  |

> Response

```json
{
"id": "id1",
"status": "ok",
"subbed": "market.btcusdt.mbp.refresh.20",
"ts": 1489474081631 //system response time
}
```

> Refresh Update

```json
{
"ch": "market.btcusdt.mbp.refresh.20",
"ts": 1573199608679, //system update time
"tick": {

		"seqNum": 100020142010,
		"bids": [
			[618.37, 71.594], // [price, size]
			[423.33, 77.726],
			[223.18, 47.997],
			[219.34, 24.82],
			[210.34, 94.463], ... // 省略余下15档
   		],
		"asks": [
			[650.59, 14.909733438479636],
			[650.63, 97.996],
			[650.77, 97.465],
			[651.23, 83.973],
			[651.42, 34.465], ... // 省略余下15档
		]
}
}
```

### 数据更新字段列表

| 字段   | 数据类型 | 描述                                    |
| ------ | -------- | --------------------------------------- |
| seqNum | integer  | 消息序列号                              |
| bids   | object   | 买盘，按price降序排列，["price","size"] |
| asks   | object   | 卖盘，按price升序排列，["price","size"] |


## 买一卖一逐笔行情

当买一价、买一量、卖一价、卖一量，其中任一数据发生变化时，此主题推送逐笔更新。

### 主题订阅

`market.$symbol.bbo`

> Subscribe request

```json
{
  "sub": "market.btcusdt.bbo",
  "id": "id1"
}
```

### 参数

| 参数   | 数据类型 | 是否必需 | 缺省值 | 描述     | 取值范围                                               |
| ------ | -------- | -------- | ------ | -------- | ------------------------------------------------------ |
| symbol | string   | true     | NA     | 交易代码 | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |

> Response

```json
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.bbo",
  "ts": 1489474081631 //system response time
}
```

> Update example

```json
{
  "ch": "market.btcusdt.bbo",
  "ts": 1489474082831, //system update time
  "tick": {
    "symbol": "btcusdt",
    "quoteTime": "1489474082811",
    "bid": "10008.31",
    "bidSize": "0.01",
    "ask": "10009.54",
    "askSize": "0.3",
    "seqId":"10242474683"
  }
}
```

### 数据更新字段列表

| 字段      | 数据类型 | 描述         |
| --------- | -------- | ------------ |
| symbol    | string   | 交易代码     |
| quoteTime | long     | 盘口更新时间 |
| bid       | float    | 买一价       |
| bidSize   | float    | 买一量       |
| ask       | float    | 卖一价       |
| askSize   | float    | 卖一量       |
| seqId     | int      | 消息序号     |


## 成交明细

### 主题订阅

此主题提供市场最新成交逐笔明细。

`market.$symbol.trade.detail`

> Subscribe request

```json
{
  "sub": "market.btcusdt.trade.detail",
  "id": "id1"
}
```

### 参数

| 参数   | 数据类型 | 是否必需 | 缺省值 | 描述     | 取值范围                                               |
| ------ | -------- | -------- | ------ | -------- | ------------------------------------------------------ |
| symbol | string   | true     | NA     | 交易代码 | btcusdt, ethbtc...（取值参考`GET /v1/common/symbols`） |

> Response

```json
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.trade.detail",
  "ts": 1489474081631 //system response time
}
```

> Update example

```json
{
  "ch": "market.btcusdt.trade.detail",
  "ts": 1489474082831, //system update time
  "tick": {
        "id": 14650745135,
        "ts": 1533265950234, //trade time
        "data": [
            {
                "amount": 0.0099,
                "ts": 1533265950234, //trade time
                "id": 146507451359183894799,
                "tradeId": 102043494568,
                "price": 401.74,
                "direction": "buy"
            }
            // more Trade Detail data here
        ]
  }
}
```

### 数据更新字段列表

| 字段      | 数据类型 | 描述                                           |
| --------- | -------- | ---------------------------------------------- |
| id        | integer  | 唯一成交ID（将被废弃）                         |
| tradeId   | integer  | 唯一成交ID（NEW）                              |
| amount    | float    | 成交量（买或卖一方）                           |
| price     | float    | 成交价                                         |
| ts        | integer  | 成交时间 (UNIX epoch time in millisecond)      |
| direction | string   | 成交主动方 (taker的订单方向) : 'buy' or 'sell' |

### 数据请求

支持数据请求方式一次性获取成交明细数据（仅能获取最多最近300个成交记录）：

```json
{
  "req": "market.btcusdt.trade.detail",
  "id": "id11"
}
```

## 市场概要

### 主题订阅

此主题提供24小时内最新市场概要快照。快照频率不超过每秒10次。

`market.$symbol.detail`

> Subscribe request

```json
{
  "sub": "market.btcusdt.detail",
  "id": "id1"
}
```

### 参数

| 参数   | 数据类型 | 是否必需 | 缺省值 | 描述     | 取值范围             |
| ------ | -------- | -------- | ------ | -------- | -------------------- |
| symbol | string   | true     | NA     | 交易代码 | btcusdt, ethbtc...等 |

> Response

```json
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.detail",
  "ts": 1489474081631 //system response time
}
```

> Update example

```json
{
  "ch": "market.btcusdt.detail",
  "ts": 1494496390001, //system update time
  "tick": {
    "amount": 12224.2922,
    "open":   9790.52,
    "close":  10195.00,
    "high":   10300.00,
    "id":     1494496390,
    "count":  15195,
    "low":    9657.00,
    "vol":    121906001.754751
  }
}
```

### 数据更新字段列表

| 字段   | 数据类型 | 描述                     |
| ------ | -------- | ------------------------ |
| id     | integer  | unix时间，同时作为消息ID |
| amount | float    | 24小时成交量             |
| count  | integer  | 24小时成交笔数           |
| open   | float    | 24小时开盘价             |
| close  | float    | 最新价                   |
| low    | float    | 24小时最低价             |
| high   | float    | 24小时最高价             |
| vol    | float    | 24小时成交额             |

### 数据请求

支持数据请求方式一次性获取市场概要数据：

```json
{
  "req": "market.btcusdt.detail",
  "id": "id11"
}
```


## 常见错误码

以下是WebSocket行情接口的错误码、错误消息和说明。

| 错误码      | 错误消息                               | 说明                     |
| ----------- | -------------------------------------- | ------------------------ |
| bad-request | invalid topic                          | topic错误                |
| bad-request | invalid symbol                         | symbol错误               |
| bad-request | symbol trade not open now              | 该交易对未到开始交易时间 |
| bad-request | 429 too many request                   | req 请求太频繁           |
| bad-request | unsub with not subbed topic            | 未订阅该主题             |
| bad-request | not json string                        | 发送的请求不是JSON格式   |
| 1008        | header required correct cloud-exchange | exchangeCode 参数错误    |
| bad-request | request timeout                        | 请求超时                 |

# Websocket资产及订单

## 简介

### 接入URL

**Websocket资产及订单**

**`wss://api.huobi.com.hk/ws/v2`**  



### 数据压缩

与行情WebSocket不同，资产和订单返回的数据未进行 GZIP 压缩。

### 心跳消息

当用户的Websocket客户端连接到火币WebSocket服务器后，服务器会定期（当前设为20秒）向其发送`Ping`消息并包含一整数值如下：

```json
{
	"action": "ping",
	"data": {
		"ts": 1575537778295
	}
}
```

当用户的Websocket客户端接收到此心跳信息后，应返回`Pong`消息并包含同一整数值：

```json
{
    "action": "pong",
    "data": {
          "ts": 1575537778295 // 使用Ping消息中的ts值
    }
}
```

### `action`的有效取值

| 有效取值   | 取值说明                             |
| ---------- | ------------------------------------ |
| sub        | 订阅数据                             |
| req        | 数据请求                             |
| ping、pong | 心跳数据                             |
| push       | 推送数据，服务端发送至客户端数据类型 |

### 限频

此版本对用户采取了多维度的限频策略，具体策略如下：

- 限制单连接**有效**的请求（包括req，sub，unsub，不包括ping/pong和其他无效请求)为**50次/秒**（此处秒限制为滑动窗口）。当超过此限制时，会返回"too many request"错误消息。
- 限制单API Key建连总数为**10**。当超过此限制时，会返回"too many connection"错误消息。
- 限制单IP建立连接数为**100次/秒**。当超过次限制时，会返回"too many request"错误消息。

### 鉴权

鉴权请求格式如下：

```json
{
    "action": "req", 
    "ch": "auth",
    "params": { 
        "authType":"api",
        "accessKey": "e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx",
        "signatureMethod": "HmacSHA256",
        "signatureVersion": "2.1",
        "timestamp": "2019-09-01T18:16:16",
        "signature": "4F65x5A2bLyMWVQj3Aqp+B4w+ivaA7n5Oi2SuYtCJ9o="
    }
}

```

鉴权成功后返回数据格式如下：

```json
{
	"action": "req",
	"code": 200,
	"ch": "auth",
	"data": {}
}
```

参数说明

| 字段             | 是否必需 | 数据类型 | 描述                                                         |
| ---------------- | -------- | -------- | ------------------------------------------------------------ |
| action           | true     | string   | Websocket数据操作类型，鉴权固定值为req                       |
| ch               | true     | string   | 请求主题，鉴权固定值为auth                                   |
| authType         | true     | string   | 鉴权类型，鉴权固定值为api。注意，该参数不在签名计算中。      |
| accessKey        | true     | string   | 您申请的API Key中的AccessKey                                 |
| signatureMethod  | true     | string   | 签名方法，用户计算签名寄语哈希的协议，固定值为HmacSHA256     |
| signatureVersion | true     | string   | 签名协议版本，固定值为2.1                                    |
| timestamp        | true     | string   | 时间戳，您发出请求的时间（UTC时间）在查询请求中包含此值有助于防止第三方截取您的请求。如：2017-05-11T16:22:06。再次强调是 (UTC 时区) |
| signature        | true     | string   | 签名, 计算得出的值，用于确保签名有效和未被篡改               |

### 签名步骤

资产和订单WebSocket签名与Rest接口签名步骤相似，具体区别如下：

1. 生成参与签名的字符串时，请求方法固定使用GET，请求地址固定为/ws/v2

2. 生成参与签名的固定参数名替换为：accessKey，signatureMethod，signatureVersion，timestamp

3. signatureVersion版本升级为2.1



签名前最后生成的字符串如下：

```
GET\n
api.huobi.com.hk\n
/ws/v2\n
accessKey=0664b695-rfhfg2mkl3-abbf6c5d-49810&signatureMethod=HmacSHA256&signatureVersion=2.1&timestamp=2019-12-05T11%3A53%3A03
```

注：JSON请求中的数据不需要URL编码。

### 订阅主题

成功建立与Websocket服务器的连接后，Websocket客户端发送如下请求以订阅特定主题：

```json
{
	"action": "sub",
	"ch": "accounts.update"
}
```
订阅成功Websocket客户端会接收到如下消息：

```json
{
	"action": "sub",
	"code": 200,
	"ch": "accounts.update#0",
	"data": {}
}
```

### 请求数据

成功建立Websocket服务器的连接后，Websocket客户端发送如下请求用以获取一次性数据：


```json
{
    "action": "req", 
    "ch": "topic",
}
```

请求成功后Websocket客户端会收到如下消息：

```json
{
    "action": "req",
    "ch": "topic",
    "code": 200,
    "data": {} // 请求数据体
}
```

## 订阅订单更新

API Key 权限：读取

订单的更新推送由任一以下事件触发：<br>
-	计划委托或追踪委托触发失败事件（eventType=trigger）<br>
- 计划委托或追踪委托触发前撤单事件（eventType=deletion）<br>
- 订单创建（eventType=creation）<br>
-	订单成交（eventType=trade）<br>
-	订单撤销（eventType=cancellation）<br>

不同事件类型所推送的消息中，字段列表略有不同。开发者可以采取以下两种方式设计返回的数据结构：<br>
- 定义一个包含所有字段的数据结构，并允许某些字段为空<br>
- 定义不同的数据结构，分别包含各自的字段，并继承自一个包含公共数据字段的数据结构

### 订阅主题

` orders#${symbol}`

### 订阅参数

> Subscribe request

```json
{
	"action": "sub",
	"ch": "orders#btcusdt"
}

```

> Response

```json
{
	"action": "sub",
	"code": 200,
	"ch": "orders#btcusdt",
	"data": {}
}
```

| 参数   | 数据类型 | 描述                      |
| ------ | -------- | ------------------------- |
| symbol | string   | 交易代码（支持通配符 * ） |


### 数据更新字段列表

> Update example

```json
{
	"action":"push",
	"ch":"orders#btcusdt",
	"data":
	{
		"orderSide":"buy",
		"lastActTime":1583853365586,
		"clientOrderId":"abc123",
		"orderStatus":"rejected",
		"symbol":"btcusdt",
		"eventType":"trigger",
		"errCode": 2002,
		"errMessage":"invalid.client.order.id (NT)"
	}
}
```

当计划委托/追踪委托触发失败后 –

| 字段          | 数据类型 | 描述                                                         |
| ------------- | -------- | ------------------------------------------------------------ |
| eventType     | string   | 事件类型，有效值：trigger（本事件仅对计划委托/追踪委托有效） |
| symbol        | string   | 交易代码                                                     |
| clientOrderId | string   | 用户自编订单号                                               |
| orderSide     | string   | 订单方向，有效值：buy,sell                                   |
| orderStatus   | string   | 订单状态，有效值：rejected                                   |
| errCode       | int      | 订单触发失败错误码                                           |
| errMessage    | string   | 订单触发失败错误消息                                         |
| lastActTime   | long     | 订单触发失败时间                                             |

> Update example

```json
{
	"action":"push",
	"ch":"orders#btcusdt",
	"data":
	{
		"orderSide":"buy",
		"lastActTime":1583853365586,
		"clientOrderId":"abc123",
		"orderStatus":"canceled",
		"symbol":"btcusdt",
		"eventType":"deletion"
	}
}
```

当计划委托/追踪委托在触发前被撤销后 –

| 字段          | 数据类型 | 描述                                                         |
| ------------- | -------- | ------------------------------------------------------------ |
| eventType     | string   | 事件类型，有效值：deletion（本事件仅对计划委托/追踪委托有效） |
| symbol        | string   | 交易代码                                                     |
| clientOrderId | string   | 用户自编订单号                                               |
| orderSide     | string   | 订单方向，有效值：buy,sell                                   |
| orderStatus   | string   | 订单状态，有效值：canceled                                   |
| lastActTime   | long     | 订单撤销时间                                                 |

> Update example

```json
{
	"action":"push",
	"ch":"orders#btcusdt",
	"data":
	{
		"orderSize":"2.000000000000000000",
		"orderCreateTime":1583853365586,
		"accountId":992701,
		"orderPrice":"77.000000000000000000",
		"type":"sell-limit",
		"orderId":27163533,
		"clientOrderId":"abc123",
		"orderSource":"spot-api",
		"orderStatus":"submitted",
		"symbol":"btcusdt",
		"eventType":"creation"
	}
}
```

当订单挂单后 –

| 字段            | 数据类型 | 描述                                                         |
| --------------- | -------- | ------------------------------------------------------------ |
| eventType       | string   | 事件类型，有效值：creation                                   |
| symbol          | string   | 交易代码                                                     |
| accountId       | long     | 账户ID                                                       |
| orderId         | long     | 订单ID                                                       |
| clientOrderId   | string   | 用户自编订单号（如有）                                       |
| orderSource     | string   | 订单来源                                                     |
| orderPrice      | string   | 订单价格                                                     |
| orderSize       | string   | 订单数量（对市价买单无效）                                   |
| orderValue      | string   | 订单金额（仅对市价买单有效）                                 |
| type            | string   | 订单类型，有效值：buy-market, sell-market, buy-limit, sell-limit, buy-limit-maker, sell-limit-maker, buy-ioc, sell-ioc, buy-limit-fok, sell-limit-fok |
| orderStatus     | string   | 订单状态，有效值：submitted                                  |
| orderCreateTime | long     | 订单创建时间                                                 |

注：<br>
- 止盈止损订单在尚未被触发时，接口将不会推送此订单的创建；<br>
- Taker订单在成交前，接口首先推送其创建事件。<br>
- 止盈止损订单的订单类型不再是原始订单类型“buy-stop-limit”或“sell-stop-limit”，而是变为“buy-limit”或“sell-limit”。<br>

> Update example

```json
{
	"action":"push",
	"ch":"orders#btcusdt",
	"data":
	{
		"tradePrice":"76.000000000000000000",
		"tradeVolume":"1.013157894736842100",
		"tradeId":301,
		"tradeTime":1583854188883,
		"aggressor":true,
		"remainAmt":"0.000000000000000400000000000000000000",
		"execAmt":"2",
		"orderId":27163536,
		"type":"sell-limit",
		"clientOrderId":"abc123",
		"orderSource":"spot-api",
		"orderPrice":"15000",
		"orderSize":"0.01",
		"orderStatus":"filled",
		"symbol":"btcusdt",
		"eventType":"trade"
	}
}
```

当订单成交后 –

| 字段          | 数据类型 | 描述                                                         |
| ------------- | -------- | ------------------------------------------------------------ |
| eventType     | string   | 事件类型，有效值：trade                                      |
| symbol        | string   | 交易代码                                                     |
| tradePrice    | string   | 成交价                                                       |
| tradeVolume   | string   | 成交量                                                       |
| orderId       | long     | 订单ID                                                       |
| type          | string   | 订单类型，有效值：buy-market, sell-market, buy-limit, sell-limit, buy-limit-maker, sell-limit-maker, buy-ioc, sell-ioc, buy-limit-fok, sell-limit-fok |
| clientOrderId | string   | 用户自编订单号（如有）                                       |
| orderSource   | string   | 订单来源                                                     |
| orderPrice    | string   | 原始订单价（市价单无效）                                     |
| orderSize     | string   | 原始订单数量（市价买单无效）                                 |
| orderValue    | string   | 原始订单金额（仅对市价买单有效）                             |
| tradeId       | long     | 成交ID                                                       |
| tradeTime     | long     | 成交时间                                                     |
| aggressor     | bool     | 是否交易主动方，有效值： true (taker), false (maker)         |
| orderStatus   | string   | 订单状态，有效值：partial-filled, filled                     |
| remainAmt     | string   | 该订单未成交数量（市价买单为未成交金额）                     |
| execAmt       | string   | 该订单累计成交量（市价买单为成交金额）                       |

注：<br>
- 止盈止损订单的订单类型不再是原始订单类型“buy-stop-limit”或“sell-stop-limit”，而是变为“buy-limit”或“sell-limit”。<br>
- 当一张taker订单同时与对手方多张订单成交后，所产生的每笔成交（tradePrice, tradeVolume, tradeTime, tradeId, aggressor）将被分别推送（而不是合并推送一笔）。<br>

> Update example

```json
{
	"action":"push",
	"ch":"orders#btcusdt",
	"data":
	{
		"lastActTime":1583853475406,
		"remainAmt":"2.000000000000000000",
		"execAmt":"2",
		"orderId":27163533,
		"type":"sell-limit",
		"clientOrderId":"abc123",
		"orderSource":"spot-api",
		"orderPrice":"15000",
		"orderSize":"0.01",
		"orderStatus":"canceled",
		"symbol":"btcusdt",
		"eventType":"cancellation"
	}
}
```

当订单被撤销后 –

| 字段          | 数据类型 | 描述                                                         |
| ------------- | -------- | ------------------------------------------------------------ |
| eventType     | string   | 事件类型，有效值：cancellation                               |
| symbol        | string   | 交易代码                                                     |
| orderId       | long     | 订单ID                                                       |
| type          | string   | 订单类型，有效值：buy-market, sell-market, buy-limit, sell-limit, buy-limit-maker, sell-limit-maker, buy-ioc, sell-ioc, buy-limit-fok, sell-limit-fok |
| clientOrderId | string   | 用户自编订单号（如有）                                       |
| orderSource   | string   | 订单来源                                                     |
| orderPrice    | string   | 原始订单价（市价单无效）                                     |
| orderSize     | string   | 原始订单数量（市价买单无效）                                 |
| orderValue    | string   | 原始订单金额（仅对市价买单有效）                             |
| orderStatus   | string   | 订单状态，有效值：partial-canceled, canceled                 |
| remainAmt     | string   | 该订单未成交数量（市价买单为未成交金额）                     |
| execAmt       | string   | 该订单累计成交量（市价买单为成交金额）                       |
| lastActTime   | long     | 订单最近更新时间                                             |
注：<br>
- 止盈止损订单的订单类型不再是原始订单类型“buy-stop-limit”或“sell-stop-limit”，而是变为“buy-limit”或“sell-limit”。<br>

## 订阅清算后成交及撤单更新

API Key 权限：读取

仅当用户订单成交或撤销时推送。其中，订单成交为逐笔推送，如一张 taker 订单同时与多张 maker 订单成交，该接口将推送逐笔更新。但用户收到的这几笔成交消息的次序，有可能与实际的成交次序不完全一致。另外，如果一张订单的成交及撤销几乎同时发生，例如 IOC 订单成交后剩余部分被自动撤销，用户可能会先收到撤单推送，再收到成交推送。<br>

如用户需要获取依次更新的订单推送，建议订阅另一频道 orders#${symbol}。<br>

### 订阅主题

`trade.clearing#${symbol}#${mode}`

### 订阅参数

| 参数   | 数据类型 | 是否必需 | 描述                                                         |
| ------ | -------- | -------- | ------------------------------------------------------------ |
| symbol | string   | TRUE     | 交易代码（支持通配符 * ）                                    |
| mode   | int      | FALSE    | 推送模式（0 - 仅在订单成交时推送；1 - 在订单成交、撤销时均推送；缺省值：0） |

注：<br>
可选订阅参数 mode，如不填或填0，仅推送成交事件；如填1，推送成交及撤销事件。<br>

> Subscribe request

```json
{
	"action": "sub",
	"ch": "trade.clearing#btcusdt#0"
}

```

> Response

```json
{
	"action": "sub",
	"code": 200,
	"ch": "trade.clearing#btcusdt#0",
	"data": {}
}
```

> Update example

```json
{
    "ch": "trade.clearing#btcusdt#0",
    "data": {
         "eventType": "trade",
         "symbol": "btcusdt",
         "orderId": 99998888,
         "tradePrice": "9999.99",
         "tradeVolume": "0.96",
         "orderSide": "buy",
         "aggressor": true,
         "tradeId": 919219323232,
         "tradeTime": 998787897878,
         "transactFee": "19.88",
         "feeDeduct ": "0",
         "feeDeductType": "",
         "feeCurrency": "btc",
         "accountId": 9912791,
         "source": "spot-api",
         "orderPrice": "10000",
         "orderSize": "1",
         "clientOrderId": "a001",
         "orderCreateTime": 998787897878,
         "orderStatus": "partial-filled"
    }
}
```

### 数据更新字段列表（当订单成交后）

| 字段            | 数据类型 | 描述                                                         |
| --------------- | -------- | ------------------------------------------------------------ |
| eventType       | string   | 事件类型（trade）                                            |
| symbol          | string   | 交易代码                                                     |
| orderId         | long     | 订单ID                                                       |
| tradePrice      | string   | 成交价                                                       |
| tradeVolume     | string   | 成交量                                                       |
| orderSide       | string   | 订单方向，有效值： buy, sell                                 |
| orderType       | string   | 订单类型，有效值： buy-market, sell-market,buy-limit,sell-limit,buy-ioc,sell-ioc,buy-limit-maker,sell-limit-maker,buy-stop-limit,sell-stop-limit,buy-limit-fok, sell-limit-fok, buy-stop-limit-fok, sell-stop-limit-fok |
| aggressor       | bool     | 是否交易主动方，有效值： true, false                         |
| tradeId         | long     | 交易ID                                                       |
| tradeTime       | long     | 成交时间，unix time in millisecond                           |
| transactFee     | string   | 交易手续费（正值）或交易手续费返佣（负值）                   |
| feeCurrency     | string   | 交易手续费或交易手续费返佣币种（买单的交易手续费币种为基础币种，卖单的交易手续费币种为计价币种；买单的交易手续费返佣币种为计价币种，卖单的交易手续费返佣币种为基础币种） |
| feeDeduct       | string   | 交易手续费抵扣                                               |
| feeDeductType   | string   | 交易手续费抵扣类型，有效值： ht, point                       |
| accountId       | long     | 账户编号                                                     |
| source          | string   | 订单来源                                                     |
| orderPrice      | string   | 订单价格 （市价单无此字段）                                  |
| orderSize       | string   | 订单数量（市价买单无此字段）                                 |
| orderValue      | string   | 订单金额（仅市价买单有此字段）                               |
| clientOrderId   | string   | 用户自编订单号                                               |
| stopPrice       | string   | 订单触发价（仅止盈止损订单有此字段）                         |
| operator        | string   | 订单触发方向（仅止盈止损订单有此字段）                       |
| orderCreateTime | long     | 订单创建时间                                                 |
| orderStatus     | string   | 订单状态，有效值：filled, partial-filled                     |

注：<br>
- transactFee中的交易返佣金额可能不会实时到账；<br>

### 数据更新字段列表（当订单撤销后）

| 字段            | 数据类型 | 描述                                                         |
| --------------- | -------- | ------------------------------------------------------------ |
| eventType       | string   | 事件类型（cancellation）                                     |
| symbol          | string   | 交易代码                                                     |
| orderId         | long     | 订单ID                                                       |
| orderSide       | string   | 订单方向，有效值： buy, sell                                 |
| orderType       | string   | 订单类型，有效值： buy-market, sell-market,buy-limit,sell-limit,buy-ioc,sell-ioc,buy-limit-maker,sell-limit-maker,buy-stop-limit,sell-stop-limit,buy-limit-fok, sell-limit-fok, buy-stop-limit-fok, sell-stop-limit-fok |
| accountId       | long     | 账户编号                                                     |
| source          | string   | 订单来源                                                     |
| orderPrice      | string   | 订单价格 （市价单无此字段）                                  |
| orderSize       | string   | 订单数量（市价买单无此字段）                                 |
| orderValue      | string   | 订单金额（仅市价买单有此字段）                               |
| clientOrderId   | string   | 用户自编订单号                                               |
| stopPrice       | string   | 订单触发价（仅止盈止损订单有此字段）                         |
| operator        | string   | 订单触发方向（仅止盈止损订单有此字段）                       |
| orderCreateTime | long     | 订单创建时间                                                 |
| remainAmt       | string   | 未成交量（对于市价买单，该字段定义为未成交额）               |
| orderStatus     | string   | 订单状态，有效值：canceled, partial-canceled                 |


## 订阅账户变更

API Key 权限：读取

订阅账户的余额更新。

### 订阅主题

`accounts.update#${mode}`

用户可选择以下任一账户变更推送的触发方式

1、仅在账户余额发生变动时推送；

2、在账户余额发生变动或可用余额发生变动时均推送，且分别推送。

3、在账户余额发生变动或可用余额发生变动时均推送，且一起推送。

注意：现货和合约之间的账户划转引起的账户余额变动，暂时没有消息推送。

### 订阅参数

| 参数 | 数据类型 | 描述                                 |
| ---- | -------- | ------------------------------------ |
| mode | integer  | 推送方式，有效值：0，1，2  默认值：0 |

订阅示例  

1、不填mode：  

accounts.update  

仅当账户余额变动时推送；  

2、填写mode=0：  

accounts.update#0  

仅当账户余额变动时推送；  

3、填写mode=1： 

accounts.update#1  

在账户余额发生变动或可用余额发生变动时均推送且分别推送。

4、填写mode=2：  

accounts.update#2

 在账户余额发生变动或可用余额发生变动时均推送且一起推送。

注：无论用户采用哪种模式订阅，在订阅成功后，服务器将首先推送当前各账户的账户余额与可用余额，然后再推送后续的账户更新。在首推各账户初始值时，消息中的changeType和changeTime的值为null。

> Subscribe request

```json
{
	"action": "sub",
	"ch": "accounts.update"
}
```

> Response

```json
{
	"action": "sub",
	"code": 200,
	"ch": "accounts.update#0",
	"data": {}
}
```

> Update example

```json
accounts.update#0：
{
	"action": "push",
	"ch": "accounts.update#0",
	"data": {
		"currency": "btc",
		"accountId": 123456,
		"balance": "23.111",
		"changeType": "transfer",
           	"accountType":"trade",
		"changeTime": 1568601800000
	}
}

accounts.update#1：
{
	"action": "push",
	"ch": "accounts.update#1",
	"data": {
		"currency": "btc",
		"accountId": 33385,
		"available": "2028.699426619837209087",
		"changeType": "order.match",
         		"accountType":"trade",
		"changeTime": 1574393385167
	}
}
{
	"action": "push",
	"ch": "accounts.update#1",
	"data": {
		"currency": "btc",
		"accountId": 33385,
		"balance": "2065.100267619837209301",
		"changeType": "order.match",
           	"accountType":"trade",
		"changeTime": 1574393385122
	}
}
```

### 数据更新字段列表

| 字段        | 数据类型 | 描述                                                         |
| ----------- | -------- | ------------------------------------------------------------ |
| currency    | string   | 币种                                                         |
| accountId   | long     | 账户ID                                                       |
| balance     | string   | 账户余额（仅当账户余额发生变动时推送）                       |
| available   | string   | 可用余额（仅当可用余额发生变动时推送）                       |
| changeType  | string   | 余额变动类型，有效值：order-place(订单创建)，order-match(订单成交)，order-refund(订单成交退款)，order-cancel(订单撤销)，deposit (充币）, withdraw (提币)，other(其他资产变化) |
| accountType | string   | 账户类型，有效值：trade, loan, interest                      |
| changeTime  | long     | 余额变动时间，unix time in millisecond                       |

注：<br>
账户更新推送的是到账金额，多笔成交产生的多笔交易返佣可能会合并到帐。<br>

## 常见错误码

以下是WebSocket资产和订单接口的错误码、错误消息和说明。

| 错误码 | 错误消息                 | 说明                                                         |
| ------ | ------------------------ | ------------------------------------------------------------ |
| 200    | 正确                     | 正确返回                                                     |
| 100    | 超时关闭                 | 超时关闭                                                     |
| 400    | Bad Request              | 请求错误                                                     |
| 404    | Not Found                | 找不到服务                                                   |
| 429    | Too Many Requests        | 超过单机服务最大连接数或者超过单IP最大连接数                 |
| 500    | 系统异常                 | 系统错误                                                     |
| 2000   | invalid.ip               | 无效的ip                                                     |
| 2001   | nvalid.json              | 无效的请求json                                               |
| 2001   | invalid.authType         | 验签方式错误                                                 |
| 2001   | invalid.action           | 无效的订阅事件                                               |
| 2001   | invalid.symbol           | 无效的交易对                                                 |
| 2001   | invalid.ch               | 无效的订阅                                                   |
| 2001   | invalid.exchange         | 无效的交易所code                                             |
| 2001   | missing.param.auth       | 缺少验签参数                                                 |
| 2002   | invalid.auth.state       | 认证未通过                                                   |
| 2002   | auth.fail                | 验签失败，包括API Key绑定的IP错误                            |
| 2003   | query.account.list.error | 查询账户列表失败                                             |
| 4000   | too.many.request         | 客户端上行请求限频（单个实例50次/秒）                        |
| 4000   | too.many.connection      | 同一个API Key的建联数量超过服务器单个实例限制（单个实例最多连接10个） |

