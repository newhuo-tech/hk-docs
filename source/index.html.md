# Introduction

Welcome to Huobi Hong Kong API！  

This is the official Huobi Hong Kong API document, and will be continue updating. Huobi Hong Kong will also publish API announcement in advance for any API change. Please subscribe to our announcements so that you can get the latest updates.


**How to read this document**

The top of the document is the navigation menu for different API business; The language button in the top right is for different languages, it supports Chinese and English right now.
The main content of each API document has three parts, the left hand side is the contents, the middle part is the document body, and the right hand side is the request and response sameple.

Below is the content for Spot API document

The first part is the overview:

- **Quick Start**: It introduces the overall knowledge of Huobi Hong Kong API, and suitability for new Huobi Hong Kong API user
- **FAQ**: It lists the frequently asked questions regardless the specific API


The second part is detail for each API. Each API category is listed in one section, and each each section has below contents:

- **Introduction**: It introduces notes and description for this API category
- ***Specific API***: It introduces the usage, rate limit, request, parameters and response for each API
- **Error Code**: It lists the common error code and the description for this API category
- **FAQ**: It lists the frequently asked questions for this API category

# Quick Start

## Preparation

Before you use API, you need to login the website to create API Key with proper permissions. The API key is shared for all instruments in Huobi Hong Kong including spot, futures, swap, options.

You can manage your API Keys <a href='https://www.hbg.com/zh-cn/apikey/'>here</a>.

Every user can create at most 20 API Keys, each can be applied with either permission below:

- Read permission: It is used to query the data, such as order query, trade query.
- Trade permission: It is used to create order, cancel order and transfer, etc.
- Withdraw permission: It is used to create withdraw order, cancel withdraw order, etc.

Please remember below information after creation:

- `Access Key`  It is used in API request
  
- `Secret Key`  It is used to generate the signature (only visible once after creation)

~~~~
The API Key can bind maximum 20 IP addresses (either host IP or network IP), we strongly suggest you bind IP address for security purpose. The API Key without IP binding will be expired after 90 days.
~~~~


~~~~
Warning: These two keys are important to your account safety, please don't share <b>both</b> of them together to anyone else (including any product or person from Huobi). If you find your API Key is disposed, please remove it immediately.
~~~~


## Interface Type

There are two types of interface, you can choose the proper one according to your scenario and preferences.

### REST API

REST (Representational State Transfer) is one of the most popular communication mechanism under HTTP, each URL represents a type of resource.

It is suggested to use Rest API for one-off operation, like trading and withdraw.

### WebSocket API

WebSocket is a new protocol in HTML5. It is full-duplex between client and server. The connection can be established by a single handshake, and then server can push the notification to client actively.

It is suggest to use WebSocket API to get data update, like market data and order update.

**Authentication**

Both API has two levels of authentication:

Public API: It is for basic information and market data. It doesn't need authentication.

Private API: It is for account related operation like trading and account management. Each private API must be authenticated with API Key.

## Access URLs
You can compare the network latency between two domain <u>api.huobi.com.hk</u> and <u>api-aws.huobi.com.hk</u>, and then choose the better one for you.

In general, the domain <u>api-aws.huobi.com.hk</u> is optimized for AWS client, the latency will be lower.

**REST API**

**`https://api.huobi.com.hk`**  

**Websocket Feed (market data except MBP incremental)**

**`wss://api.huobi.com.hk/ws`**  

**Websocket Feed (market data only MBP incremental)**

**`wss://api.huobi.com.hk/feed`**  

**Websocket Feed (account and order)**

**`wss://api.huobi.com.hk/ws/v2`**  
 
<aside class="notice">
Please initiate API calls with non-China IP.
</aside>
<aside class="notice">
It is not recommended to use proxy to access Huobi Hong Kong API because it will introduce high latency and low stability.
</aside>
<aside class="notice">
It is recommended to access Huobi Hong Kong API from AWS Hong Kong for better stability. 
</aside> 

## Authentication

### Overview

The API request may be tampered during internet, therefore all private API must be signed by your API Key (Secrete Key).

Each API Key has permission property, please check the API permission, and make sure your API key has proper permission.

A valid request consists of below parts:

- API Path: for example <u>api.huobi.com.hk/v1/order/orders</u>
- API Access Key: The 'Access Key' in your API Key
- Signature Method: The Hash method that is used to sign, it uses **HmacSHA256**
- Signature Version: The version for the signature protocol, it uses **2**
- Timestamp: The UTC time when the request is sent, e.g. 2017-05-11T16:22:06. It is useful to prevent the request to be intercepted by third-party.
- Parameters: Each API Method has a group of parameters, you can refer to detailed document for each of them. 
  - For GET request, all the parameters must be signed.
  - For POST request, the parameters needn't be signed and they should be put in request body.
- Signature: The value after signed, it is guarantee the signature is valid and the request is not be tempered.

### Signature Method

The signature may be different if the request text is different, therefore the request should be normalized before signing. Below signing steps take the order query as an example:

This is a full URL to query one order:

`https://api.huobi.com.hk/v1/order/orders?`

`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx`

`&SignatureMethod=HmacSHA256`

`&SignatureVersion=2`

`&Timestamp=2021-05-11T15:19:30`

`&order-id=1234567890`

**1. The request Method (GET or POST, WebSocket use GET), append line break “\n”**

`GET`

**2. The host with lower case, append line break “\n”**

Example:
`
api.huobi.com.hk
`

**3. The path, append line break “\n”**

For example, query orders:

`
/v1/order/orders
`



For example, WebSocket v2

`/ws/v2`

**4. The parameters are URL encoded, and ordered based on ASCII**

For example below is the original parameters:

`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx`

`order-id=1234567890`

`SignatureMethod=HmacSHA256`

`SignatureVersion=2`

`Timestamp=2021-05-11T15%3A19%3A30`

<aside class="notice">
Use UTF-8 encoding and URL encoded, the hex must be upper case. For example, The semicolon ':' should be encoded as '%3A', The space should be encoded as '%20'.
</aside>
<aside class="notice">
The 'timestamp' should be formated as 'YYYY-MM-DDThh:mm:ss' and URL encoded. The value is valid within 5 minutes.
</aside>

Then above parameter should be ordered like below:


`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx`

`SignatureMethod=HmacSHA256`

`SignatureVersion=2`

`Timestamp=2021-05-11T15%3A19%3A30`

`order-id=1234567890`

**5. Use char  “&” to concatenate all parameters**


`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=2021-05-11T15%3A19%3A30&order-id=1234567890`

**6. Assemble the pre-signed text**

`GET`

`api.huobi.com.hk`

`/v1/order/orders`

`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=2021-05-11T15%3A19%3A30&order-id=1234567890`


**7. Use the pre-signed text and your Secret Key to generate a signature**

- Use the pre-signed text in step 6 and your API Secret Key to generate hash code by HmacSHA256 hash function.
- Encode the hash code with base-64 to generate the signature.

`4F65x5A2bLyMWVQj3Aqp+B4w+ivaA7n5Oi2SuYtCJ9o=`

**8. Put the signature into request URL**

For Rest interface:

1. Put all the parameters in the URL
2. Encode signature by URL encoding and append in the URL with parameter name “Signature”.

Finally, the request sent to API should be:

`https://api.huobi.com.hk/v1/order/orders?AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&order-id=1234567890&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=2021-05-11T15%3A19%3A30&Signature=4F65x5A2bLyMWVQj3Aqp%2BB4w%2BivaA7n5Oi2SuYtCJ9o%3D`



For WebSocket interface:

1. Fill the value according to required JSON schema
2. The value in JSON doesn't require URL encode

For example:

`{
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
}`

## Sub User

Sub user can be used to isolate the assets and trade, the assets can be transferred between parent and sub users. Sub user can only trade with the sub user. The assets can not be transferred between sub users, only parent user has the transfer permission.  

Sub user has independent login password and API Key, they are managed under parent user in website.

Each parent user can create **200** sub user, each sub user can create at most **20** API Key, each API key can have two permissions: **read** and **trade**.

The sub user API Key can also bind IP address, the expiry policy is the same with parent user.

The sub user can access all public API (including basic information and market data), below are the private APIs that sub user can access:

| API                                                          | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [POST /v1/order/orders/place](#fd6ce2a756)                   | Create and execute an order                                  |
| [POST /v1/order/orders/{order-id}/submitcancel](#4e53c0fccd) | Cancel an order                                              |
| [POST /v1/order/orders/submitCancelClientOrder](#submit-cancel-for-an-order-based-on-client-order-id) | Cancel an Order based on client order ID                     |
| [POST /v1/order/orders/batchcancel](#ad00632ed5)             | Cancel multiple orders                                       |
| [POST /v1/order/orders/batchCancelOpenOrders](#open-orders)  | Cancel the open orders                                       |
| [GET /v1/order/orders/{order-id}](#92d59b6aad)               | Query a specific order                                       |
| [GET /v1/order/orders](#d72a5b49e7)                          | Query orders with criteria                                   |
| [GET /v1/order/openOrders](#95f2078356)                      | Query open orders                                            |
| [GET /v1/order/matchresults](#0fa6055598)                    | Query the order matching result                              |
| [GET /v1/order/orders/{order-id}/matchresults](#56c6c47284)  | Query a specific order matching result                       |
| [GET /v1/account/accounts](#bd9157656f)                      | Query all accounts in current user                           |
| [GET /v1/account/accounts/{account-id}/balance](#870c0ab88b) | Query the specific account balance                           |


<aside class="notice">
All other APIs couldn't be accessed by sub user, otherwise the API will return “error-code 403”。
</aside>

## Glossary

### Trading symbols

The trading symbols are consist of base currency and quote currency. Take the symbol `BTC/USDT` as an example, `BTC` is the base currency, and `USDT` is the quote currency.  

### Account

The `account-id` defines the Identity for different business type, it can be retrieved from API `/v1/account/accounts` , where the `account-type` is the business types.


# API Access

## Overview

| Category     | URL Path                     | Description                                                  |
| ------------ | ---------------------------- | ------------------------------------------------------------ |
| Common       | /v1/common/*                 | Common interface, including currency, currency pair, timestamp, etc |
| Market Data  | /market/*                    | Market data interface, including trading, depth, quotation, etc |
| Account      | /v1/account/*  /v1/subuser/* | Account interface, including account information, sub-user ,etc |
| Order        | /v1/order/*                  | Order interface, including order creation, cancellation, query, etc |

Above is a general category, it doesn't cover all API, you can refer to detailed API document according to your requirement.


## Rate Limiting Rule

Except those endpoints which are marked with new rate limit value separately, following rate limit rules are applicable -
* Each API Key is limited to 10 times per second
* If API Key is empty in request, then each IP is limited to 10 times per second

Endpoints marked with rate limit value separately are applied with new rate limit rule. See "new version rate limit rule" sector of this document.

For example

* Order interface is limited by API Key: no more than 10 times within 1 sec
* Market data interface is limited by IP: no more than 10 times within 1 sec

## Request Format

The API is restful and there are two method: GET and POST.

* GET request: All parameters are included in URL
* POST request: All parameters are formatted as JSON and put int the request body

## Response Format
### Response v1
The response is JSON format.There are four fields in the top level: `status`, `ch`, `ts` and `data`. The first three fields indicate the general status, the business data is is under `data` field.

Below is an example of response:

```json
{
  "status": "ok",
  "ch": "market.btcusdt.kline.1day",
  "ts": 1499223904680,
  "data": // per API response data in nested JSON object
}
```

| Field  | Data Type | Description                                                  |
| ------ | --------- | ------------------------------------------------------------ |
| status | string    | Status of API response                                       |
| ch     | string    | The data stream. It may be empty as some API doesn't have data stream |
| ts     | int       | The UTC timestamp when API respond, the unit is millisecond  |
| data   | object    | The body data in response                                    |

### Response v2
For v2, there are three fields in the top level: `code`, `message` and `data`. The first two fields indicate the general status, the business data is is under `data` field.

##  Data Type

The JSON data type described in this document is defined as below:

- `string`: a sequence of characters that are quoted
- `int`: a 32-bit integer, mainly used for status code, size and count
- `long`: a 64-bit integer, mainly used for Id and timestamp
- `float`: a fraction represented in decimal format, mainly used for volume and price, recommend to use high precision decimal data types in program
- `object`: An object, which contains one sub object {}
- `array` : Array, contains multiple objects

## Best Practice

### Security
- It is strongly suggested to bind your IP with your API Key to ensure that your API Key can only be used in your machine. Furthermore, your API Key will be expired after 90 days if it is not binded with any IP.
- It is strongly suggested not to share your API Key with any body or third-party software, otherwise your personal information and asset may be stolen. If your expose your API Key by accident, please do delete the API Key and create a new one.

### General
**API Access**

- It is suggested not to use temporary domain or proxy, which may be not stable.
- It is suggested to use AWS Hong Kong to access API for lower latency


### Market

**Market data**

- It is suggested to use WebSocket interface to subscribe the market update and then cache the data locally, because WebSocket notification has lower latency and not have rate limit.
- It is suggested not to subscribe too many topics in a single websocket connection, it may generate more notifications and cause network latency and disconnection.

**Latest trade**

- It is suggested to subscribe WebSocket topic `market.$symbol.trade.detail`, the response field `price` represents the latest price, and it has lower latency.
- It is suggested to use `tradeId` to de-duplicate if you subscribe WebSocket topic `market.$symbol.trade.detail`.

**Depth**

- It is suggested to subscribe WebSocket topic `market.$symbol.bbo` if you only need the best bid and best offer.
- It is suggested to subscribe WebSocket topic `market.$symbol.depth.$type` if you need multiple bid and offer with normal latency.
- It is suggested to subscribe WebSocket topic `market.$symbol.mbp.$level` if you need multiple bid and offer with lower latency
- It is suggested to use `version` field to de-duplicate and discard the smaller data if you use Rest interface `/market/depth` and WebSocket topic `market.$symbol.depth.$type`. It is suggest to use `seqNum` to de-duplicate and discard the smaller data if yo subscribe WebSocket topic `market.$symbol.mbp.$levels`.

###Order

**Place an order (/v1/order/orders/place)**

- It is suggested to follow the symbol reference (`/v1/common/symbols`) to validate the amount and value before placing the older, otherwise you may place an invalid order and waste your time.
- It is suggested to provide an unique `client-order-id` field when placing the order, it is useful to track your orders status if you fail to get the order id response. Later you can use the `client-order-id` to match the WebSocket order notification or query order detail by interface `/v1/order/orders/getClientOrder`.The uniqueness of the clientOrderId passed in when you place an order will no longer be verified. We recommend you to manage clientOrderId by yourself to ensure its uniqueness. If multiple orders use the same clientOrderId, the latest order corresponding to the clientOrderId will be returned when querying/canceling an order.

**Search history olders (/v1/order/orders)**

- It is recommended to use `start-time` and `end-time` to query, that are two timestamps with 13 digits (millisecond). The maximum query time window is 48 hours (2 days), the more precision you provide, the better performance you will get. You can query for multiple iterations. 

**Order update**

- It is suggested to subscribe WebSocket topic `orders.$symbol.update`, which has lower latency and more accurate sequence.
- It is suggested not to subscribe WebSocket topic `orders.$symbol`,which is replaced by `orders.$symbol.update`, and will be retired later.

###Account

**Asset update**

- It is suggested to subscribe both WebSocket topic `orders.$symbol.update` and `account.update#${mode}`. The former one tells the order status update and arrives earlier than the latter one, and the latter one confirms the final asset balance.
- It is suggested not to subscribe WebSocket topic `accounts`, which is replaced by `accounts.update#${mode}`, and will be retired later.


# Frequently Asked Questions

This section lists the  frequently asked questions regardless the specific API, such as network, signature or common errors.

For specific API question, please check the Error Code and FAQ in each API category.

### Q1：What is UID and account-id?
UID is the unique ID for a user (including master user and sub user), it can be found in Web or App personal information part, or retrieved from API `GET /v2/user/uid`.

The `account-id` defines the identity for different account type under one user, it can be retrieved from API `/v1/account/accounts` , where the `account-type` is the account types.

The types include but not limited to below types, contract account types (futures/swap/option) are not included:

- spot: Spot account
- otc: OTC account
- margin: Isolated margin account, the detailed currency type is defined in `subType`
- super-margin / cross-margin:  Cross-margin account
- investment: c2c margin lending account
- borrow: c2c margin borrowing account
- point: Point card account
- minepool: Minepool account
- etf: ETF account

### Q2：How many API Keys one user can apply?

Every user can create 20 API Keys, and each API Key can be granted with 2 permissions: **read** and **trade**

Each user could create up to 200 sub users, and each sub user could create 20 API Keys, each API key can be granted with 2 permissions: **read** and **trade**.

Below are the explanation for permissions:

1. Read permission: It is used to query data, for example, **query orders**, **query trades**.
2. Trade permission: it is used to **place order**, **cancel order** and **transfer**.
3. Withdraw permission: it is used to **withdraw**, **cancel withdraw**.

### Q3：Why APIs are always disconnected or timeout?

Please follow below suggestions:

1. It is suggested to invoke API only to host <u>api.huobi.com.hk</u>

### Q4：Why the WebSocket is often disconnected?

Please check below possible reasons:

1. The client didn't respond 'Pong'. It is requird to respond 'Pong' after receive 'Ping' from server.
2. The server didn't receive 'Pong' successfully due to network issue.
3. The connection is broken due to network issue.
4. It is suggested to implement WebSocket re-connect mechanism. If Ping/Pong works well but the connection is broken, the application should be able to re-connect automatically.


### Q5：Why the signature authentication always fail?

Please check whether you follow below rules:

1.The parameter in signature text should be ordered by ASCII, for example below is the original parameters:

`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx`

`order-id=1234567890`

`SignatureMethod=HmacSHA256`

`SignatureVersion=2`

`Timestamp=2021-05-11T15%3A19%3A30`

They should be ordered like below:

`AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx`

`SignatureMethod=HmacSHA256`

`SignatureVersion=2`

`Timestamp=2021-05-11T15%3A19%3A30`

`order-id=1234567890`

2.The signature text should be URL encoded, for example

- The semicolon `:`should be encoded as `%3A`, The space should be encoded as `%20`.
- The timestamp should be formatted as `YYYY-MM-DDThh:mm:ss` and after encoded it should be like `2017-05-11T15%3A19%3A30`  

3.The signature should be base64 encoded.

4.The parameter for Get request should be included in signature request.

5.The Timestamp should be UTC time and the format should be YYYY-MM-DDTHH:mm:ss.

6.The time difference between your timestamp and standard should be less than 1 minute.

7.The message body doesn't need URL encoded if you are using WebSocket for authentication.

8.The host in signature text should be the same as the host in your API request.

The proxy may change the request host, you can try without proxy;

Some http/websocket library may include port in the host, you can try to append port in signature host, like "api.huobi.com.hk:443"


### Q6：Why the API return 'Incorrect Access Key'?

Please check whether Access Key is wrong in URL request, such as:

1. The `AccessKeyId` is not included in URL parameter
2. The length of AccessKey is wrong
3. The AccessKey is already deleted
4. The URL request is not assembled correctly which cause AccessKey is parsed unexpected in server side.

### Q7：Why the API return 'gateway-internal-error'?

Please check below possible reasons:

1. It may be due to network issue or server internal error, please try again later.
2. The data format should be correct (standard JSON).
3. The `Content-Type` in POST header should be `application/json` .

### Q8：Why the API return 'login-required'?

Please check below possible reasons:

1. The URL request parameter should include `AccessKeyId`.
2. The URL request parameter should include `Signature`.

### Q9: Why the API return HTTP 405 'method-not-allowed'?

It indicates the request path doesn't exist, please check the path spelling carefully. Due to the Nginx setting, the request path is case sensitive, please follow the path definition in document.


# Reference Data

## Introduction

Reference data APIs provide public reference information such as system status, market status, symbol info, currency info, chain info and server timestamp.

## Get all Supported Trading Symbol

This endpoint returns all Huobi Hong Kong's supported tradable symbols.

### HTTP Request

`GET /v1/common/symbols`

### Request Parameters

No parameter is needed for this endpoint.

> Responds:

### Response Content

| Parameter                  | Required | Data Type | Description                                                  |
| -------------------------- | -------- | --------- | ------------------------------------------------------------ |
| base-currency              | true     | string    | Base currency in a trading symbol                            |
| quote-currency             | true     | string    | Quote currency in a trading symbol                           |
| price-precision            | true     | integer   | Quote currency precision when quote price(decimal places)）  |
| amount-precision           | true     | integer   | Base currency precision when quote amount(decimal places)）  |
| symbol-partition           | true     | string    | Trading section, possible values: [main，innovation]         |
| symbol                     | true     | string    | symbol                                                       |
| state                      | true     | string    | The status of the symbol；Allowable values: [online，offline,suspend]. "online" - Listed, available for trading, "offline" - de-listed, not available for trading， "suspend"-suspended for trading, "pre-online" - to be online soon |
| value-precision            | true     | integer   | Precision of value in quote currency (value = price * amount) |
| min-order-amt              | true     | float     | Minimum order amount of limit order in base currency (to be obsoleted) |
| max-order-amt              | true     | float     | Max order amount of limit order in base currency (to be obsoleted) |
| limit-order-min-order-amt  | true     | float     | Minimum order amount of limit order in base currency (NEW)   |
| limit-order-max-order-amt  | true     | float     | Max order amount of limit order in base currency (NEW)       |
| sell-market-min-order-amt  | true     | float     | Minimum order amount of sell-market order in base currency (NEW) |
| sell-market-max-order-amt  | true     | float     | Max order amount of sell-market order in base currency (NEW) |
| buy-market-max-order-value | true     | float     | Max order value of buy-market order in quote currency (NEW)  |
| min-order-value            | true     | float     | Minimum order value of limit order and buy-market order in quote currency |
| max-order-value            | false    | float     | Max order value of limit order and buy-market order in usdt (NEW) |


## Get all Supported Currencies

This endpoint returns all Huobi Hong Kong's supported tradable currencies.


### HTTP Request

`GET /v1/common/currencys`

### Request Parameters

No parameter is needed for this endpoint.

> Response:

```json
  "data": [
    "usdt",
    "eth",
    "etc"
  ]
```

### Response Content

<aside class="notice">The returned "data" field contains a list of string with each string represents a suppported currency.</aside>

## APIv2 - Currency & Chains

API user could query static reference information for each currency, as well as its corresponding chain(s). (Public Endpoint)

### HTTP Request

`GET /v2/reference/currencies`


### Request Parameters

| Field Name     | Mandatory | Data Type | Description     | Value Range                                                  |
| -------------- | --------- | --------- | --------------- | ------------------------------------------------------------ |
| currency       | false     | string    | Currency        | btc, ltc, bch, eth, etc ...(available currencies in Huobi Hong Kong Global) |
| authorizedUser | false     | boolean   | Authorized user | true or false (if not filled, default value is true)         |

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

### Response Content


| Field Name              | Mandatory | Data Type | Description                                                  | Value Range            |
| ----------------------- | --------- | --------- | ------------------------------------------------------------ | ---------------------- |
| code                    | true      | int       | Status code                                                  |                        |
| message                 | false     | string    | Error message (if any)                                       |                        |
| data                    | true      | object    |                                                              |                        |
| { currency              | true      | string    | Currency                                                     |                        |
| { chains                | true      | object    |                                                              |                        |
| chain                   | true      | string    | Chain name                                                   |                        |
| displayName             | true      | string    | Chain display name                                           |                        |
| baseChain               | false     | string    | Base chain name                                              |                        |
| baseChainProtocol       | false     | string    | Base chain protocol                                          |                        |
| isDynamic               | false     | boolean   | Is dynamic fee type or not (only applicable to withdrawFeeType = fixed) | true,false             |
| numOfConfirmations      | true      | int       | Number of confirmations required for deposit success (trading & withdrawal allowed once reached) |                        |
| numOfFastConfirmations  | true      | int       | Number of confirmations required for quick success (trading allowed but withdrawal disallowed once reached) |                        |
| minDepositAmt           | true      | string    | Minimal deposit amount in each request                       |                        |
| depositStatus           | true      | string    | Deposit status                                               | allowed,prohibited     |
| minWithdrawAmt          | true      | string    | Minimal withdraw amount in each request                      |                        |
| maxWithdrawAmt          | true      | string    | Maximum withdraw amount in each request                      |                        |
| withdrawQuotaPerDay     | true      | string    | Maximum withdraw amount in a day (Singapore timezone)        |                        |
| withdrawQuotaPerYear    | true      | string    | Maximum withdraw amount in a year                            |                        |
| withdrawQuotaTotal      | true      | string    | Maximum withdraw amount in total                             |                        |
| withdrawPrecision       | true      | int       | Withdraw amount precision                                    |                        |
| withdrawFeeType         | true      | string    | Type of withdraw fee (only one type can be applied to each currency) | fixed,circulated,ratio |
| transactFeeWithdraw     | false     | string    | Withdraw fee in each request (only applicable to withdrawFeeType = fixed) |                        |
| minTransactFeeWithdraw  | false     | string    | Minimal withdraw fee in each request (only applicable to withdrawFeeType = circulated or ratio) |                        |
| maxTransactFeeWithdraw  | false     | string    | Maximum withdraw fee in each request (only applicable to withdrawFeeType = circulated or ratio) |                        |
| transactFeeRateWithdraw | false     | string    | Withdraw fee in each request (only applicable to withdrawFeeType = ratio) |                        |
| withdrawStatus}         | true      | string    | Withdraw status                                              | allowed,prohibited     |
| instStatus }            | true      | string    | Instrument status                                            | normal,delisted        |

### Status Code

| Status Code | Error Message                       | Scenario            |
| ----------- | ----------------------------------- | ------------------- |
| 200         | success                             | Request successful  |
| 500         | error                               | System error        |
| 2002        | invalid field value in "field name" | Invalid field value |

## Get Current Timestamp

This endpoint returns the current timestamp, i.e. the number of **milliseconds** that have elapsed since 00:00:00 **UTC** on 1 January 1970. 


### HTTP Request

`GET /v1/common/timestamp`

### Request Parameters

No parameter is needed for this endpoint.

> Response:

```json
  "data": 1494900087029
```

### Response Content

The returned "Data" field contains an integer representing the timestamp in milliseconds adjusted to Singapore time.

# Market Data

## Introduction

Market data APIs provide public market information such as varies of candlestick, depth and trade information.

The market data is updated **once per second**. 

## Get Klines(Candles)

This endpoint retrieves all klines in a specific range.

### HTTP Request

`GET /market/history/kline`


### Query Parameters

| Parameter | Data Type | Required | Default | Description                 | Value Range                                                  |
| --------- | --------- | -------- | ------- | --------------------------- | ------------------------------------------------------------ |
| symbol    | string    | true     | NA      | The trading symbol to query | All trading symbol supported, e.g. btcusdt, bccbtcn (to retrieve candlesticks for ETP NAV, symbol = ETP trading symbol + suffix 'nav'，for example: btc3lusdtnav) |
| period    | string    | true     | NA      | The period of each candle   | 1min, 5min, 15min, 30min, 60min, 4hour, 1day, 1mon, 1week, 1year |
| size      | integer   | false    | 150     | The number of data returns  | [1, 2000]                                                    |


```json
"data": [
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

### Response Content

| Field  | Data Type | Description                                  |
| ------ | --------- | -------------------------------------------- |
| id     | long      | The UNIX timestamp in seconds as response id |
| amount | float     | Accumulated trading volume, in base currency |
| count  | integer   | The number of completed trades               |
| open   | float     | The opening price                            |
| close  | float     | The closing price                            |
| low    | float     | The low price                                |
| high   | float     | The high price                               |
| vol    | float     | Accumulated trading value, in quote currency |

## Get Latest Aggregated Ticker

This endpoint retrieves the latest ticker with some important 24h aggregated market data.

### HTTP Request

`GET /market/detail/merged`


### Request Parameters

| Parameter | Data Type | Required | Default | Description                 | Value Range                                                  |
| --------- | --------- | -------- | ------- | --------------------------- | ------------------------------------------------------------ |
| symbol    | string    | true     | NA      | The trading symbol to query | All supported trading symbol, e.g. btcusdt, bccbtc.Refer to `/v1/common/symbols` |

> The above command returns JSON structured like this:

```json
"data": {
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

### Response Content

| Field  | Data Type | Description                                                  |
| ------ | --------- | ------------------------------------------------------------ |
| id     | long      | The internal identity                                        |
| amount | float     | Accumulated trading volume of last 24 hours (rotating 24h), in base currency |
| count  | integer   | The number of completed trades (rotating 24h)                |
| open   | float     | The opening price of last 24 hours (rotating 24h)            |
| close  | float     | The last price of last 24 hours (rotating 24h)               |
| low    | float     | The lowest price of last 24 hours (rotating 24h)             |
| high   | float     | The highest price of last 24 hours (rotating 24h)            |
| vol    | float     | Accumulated trading value of last 24 hours (rotating 24h), in quote currency |
| bid    | object    | The current best bid in format [price, size]                 |
| ask    | object    | The current best ask in format [price, size]                 |

## Get Latest Tickers for All Pairs

This endpoint retrieves the latest tickers for all supported pairs.

<aside class="notice">The returned data object can contain large amount of tickers.</aside>
### HTTP Request

`GET /market/tickers`


### Request Parameters

No parameters are needed for this endpoint.

> The above command returns JSON structured like this:

```json
"data": [  
    {  
        "open":0.044297,
        "close":0.042178,
        "low":0.040110,
        "high":0.045255,
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

### Response Content

Response content is an array of object, each object has below fields.

| Field   | Data Type | Description                                                  |
| ------- | --------- | ------------------------------------------------------------ |
| amount  | float     | The aggregated trading volume in last 24 hours (rotating 24h) |
| count   | integer   | The number of completed trades of last 24 hours (rotating 24h) |
| open    | float     | The opening price of a nature day (Singapore time)           |
| close   | float     | The closing price of a nature day (Singapore time)              |
| low     | float     | The lowest price of a nature day (Singapore time)               |
| high    | float     | The highest price of a nature day (Singapore time)              |
| vol     | float     | The aggregated trading value in last 24 hours (rotating 24h) |
| symbol  | string    | The trading symbol of this object, e.g. btcusdt, bccbtc      |
| bid     | float     | Best bid price                                               |
| bidSize | float     | Best bid size                                                |
| ask     | float     | Best ask price                                               |
| askSize | float     | Best ask size                                                |

## Get Market Depth

This endpoint retrieves the current order book of a specific pair.

### HTTP Request

`GET /market/depth`

### Request Parameters

| Parameter | Data Type | Required | Default Value | Description                                       | Value Range                              |
| --------- | --------- | -------- | ------------- | ------------------------------------------------- | ---------------------------------------- |
| symbol    | string    | true     | NA            | The trading symbol to query                       | Refer to `GET /v1/common/symbols`        |
| depth     | integer   | false    | 20            | The number of market depth to return on each side | 5, 10, 20                                |
| type      | string    | true     | step0         | Market depth aggregation level, details below     | step0, step1, step2, step3, step4, step5 |

<aside class="notice">when type is set to "step0", the default value of "depth" is 150 instead of 20.</aside>
**"type" Details**

| Value | Description                          |
| ----- | ------------------------------------ |
| step0 | No market depth aggregation          |
| step1 | Aggregation level = precision*10     |
| step2 | Aggregation level = precision*100    |
| step3 | Aggregation level = precision*1000   |
| step4 | Aggregation level = precision*10000  |
| step5 | Aggregation level = precision*100000 |

> The above command returns JSON structured like this:

```json
"tick": {
    "version": 31615842081,
    "ts": 1489464585407,
    "bids": [
      [7964, 0.0678],
      [7963, 0.9162],
      [7961, 0.1],
      [7960, 12.8898],
      [7958, 1.2],
      [7955, 2.1009],
      [7954, 0.4708],
      [7953, 0.0564],
      [7951, 2.8031],
      [7950, 13.7785],
      [7949, 0.125],
      [7948, 4],
      [7942, 0.4337],
      [7940, 6.1612],
      [7936, 0.02],
      [7935, 1.3575],
      [7933, 2.002],
      [7932, 1.3449],
      [7930, 10.2974],
      [7929, 3.2226]
    ],
    "asks": [
      [7979, 0.0736],
      [7980, 1.0292],
      [7981, 5.5652],
      [7986, 0.2416],
      [7990, 1.9970],
      [7995, 0.88],
      [7996, 0.0212],
      [8000, 9.2609],
      [8002, 0.02],
      [8008, 1],
      [8010, 0.8735],
      [8011, 2.36],
      [8012, 0.02],
      [8014, 0.1067],
      [8015, 12.9118],
      [8016, 2.5206],
      [8017, 0.0166],
      [8018, 1.3218],
      [8019, 0.01],
      [8020, 13.6584]
    ]
  }
```

### Response Content

<aside class="notice">The returned data object is under 'tick' object instead of 'data' object in the top level JSON</aside>
| Field   | Data Type | Description                                                  |
| ------- | --------- | ------------------------------------------------------------ |
| ts      | integer   | The UNIX timestamp in milliseconds is adjusted to Singapore time |
| version | integer   | Internal data                                                |
| bids    | object    | The current all bids in format [price, size]                 |
| asks    | object    | The current all asks in format [price, size]                 |


## Get the Last Trade

This endpoint retrieves the latest trade with its price, volume, and direction.

### HTTP Request

`GET /market/trade`


### Request Parameters

| Parameter | Data Type | Required | Default Value | Description                 | Value Range                       |
| --------- | --------- | -------- | ------------- | --------------------------- | --------------------------------- |
| symbol    | string    | true     | NA            | The trading symbol to query | Refer to `GET /v1/common/symbols` |

> The above command returns JSON structured like this:

```json
"tick": {
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

### Response Content

<aside class="notice">The returned data object is under 'tick' object instead of 'data' object in the top level JSON</aside>
| Parameter | Data Type | Description                                                  |
| --------- | --------- | ------------------------------------------------------------ |
| id        | integer   | The unique trade id of this trade (to be obsoleted)          |
| trade-id  | integer   | The unique trade id (NEW)                                    |
| amount    | float     | The trading volume in base currency                          |
| price     | float     | The trading price in quote currency                          |
| ts        | integer   | The UNIX timestamp in milliseconds adjusted to Singapore time |
| direction | string    | The direction of the taker trade: 'buy' or 'sell'            |

## Get the Most Recent Trades

This endpoint retrieves the most recent trades with their price, volume, and direction.

### HTTP Request

`GET /history/trade`


### Request Parameters

| Parameter | Data Type | Required | Default Value | Description                 | Value Range                                                  |
| --------- | --------- | -------- | ------------- | --------------------------- | ------------------------------------------------------------ |
| symbol    | string    | true     | NA            | The trading symbol to query | All supported trading symbol, e.g. btcusdt, bccbtc.Refer to `GET /v1/common/symbols` |
| size      | integer   | false    | 1             | The number of data returns  | [1, 2000]                                                    |

> The above command returns JSON structured like this:

```json
"data": [  
   {  
      "id":31618787514,
      "ts":1544390317905,
      "data":[  
         {  
            "amount":9.000000000000000000,
            "ts":1544390317905,
            "id":3161878751418918529341,
            "trade-id": 102043495672,
            "price":94.690000000000000000,
            "direction":"sell"
         },
         {  
            "amount":73.771000000000000000,
            "ts":1544390317905,
            "id":3161878751418918532514,
            "trade-id": 102043495673,
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
            "id":3161877698918918522622,
            "trade-id": 102043495674,
            "price":94.710000000000000000,
            "direction":"buy"
         }
      ]
   }
}
```

### Response Content

<aside class="notice">The returned data object is an array which represents one recent timestamp; each timestamp object again is an array which represents all trades occurred at this timestamp.</aside>
| Field     | Data Type | Description                                                  |
| --------- | --------- | ------------------------------------------------------------ |
| id        | integer   | The unique trade id of this trade (to be obsoleted)          |
| trade-id  | integer   | The unique trade id (NEW)                                    |
| amount    | float     | The trading volume in base currency                          |
| price     | float     | The trading price in quote currency                          |
| ts        | integer   | The UNIX timestamp in milliseconds adjusted to Singapore time |
| direction | string    | The direction of the taker trade: 'buy' or 'sell'            |

## Get the Last 24h Market Summary

This endpoint retrieves the summary of trading in the market for the last 24 hours.

<aside class="notice">It is possible that the accumulated volume and the accumulated value counted for current 24h window is smaller than the previous ones.</aside>

### HTTP Request

`GET market/detail/`

### Request Parameters

| Parameter | Data Type | Required | Default Value | Description                 | Value Range                 |
| --------- | --------- | -------- | ------------- | --------------------------- | --------------------------- |
| symbol    | string    | true     | NA            | The trading symbol to query | Refer to /v1/common/symbols |

> The above command returns JSON structured like this:

```json
"tick": {  
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

### Response Content

<aside class="notice">The returned data object is under 'tick' object instead of 'data' object in the top level JSON</aside>
| Field   | Data Type | Description                                                  |
| ------- | --------- | ------------------------------------------------------------ |
| id      | integer   | The internal identity                                        |
| amount  | float     | The aggregated trading volume in USDT of last 24 hours (rotating 24h) |
| count   | integer   | The number of completed trades of last 24 hours (rotating 24h) |
| open    | float     | The opening price of last 24 hours (rotating 24h)            |
| close   | float     | The closing price of last 24 hours (rotating 24h)               |
| low     | float     | The lowest price of last 24 hours (rotating 24h)                |
| high    | float     | The highest price of last 24 hours (rotating 24h)               |
| vol     | float     | The trading volume in base currency of last 24 hours (rotating 24h) |
| version | integer   | Internal data                                                |


## Error Code

Below is the error code, error message and description returned by Market data APIs.

| Error Code        | Error Message                       | Description                                      |
| ----------------- | ----------------------------------- | ------------------------------------------------ |
| invalid-parameter | invalid symbol                      | Parameter symbol is invalid                      |
| invalid-parameter | invalid period                      | Parameter period is invalid for candlestick data |
| invalid-parameter | invalid depth                       | Parameter depth is invalid for depth data        |
| invalid-parameter | invalid type                        | Parameter type is invalid                        |
| invalid-parameter | invalid size                        | Parameter size is invalid                        |
| invalid-parameter | invalid size,valid range: [1, 2000] | Parameter size range is invalid                  |
| invalid-parameter | request timeout                     | Request timeout please try again                 |

# Account

## Introduction

Account APIs provide account related (such as basic info, balance, history, point) query and transfer functionality.

<aside class="notice">All endpoints in this section require authentication</aside>

## Get all Accounts of the Current User

API Key Permission：Read<br>
Rate Limit (NEW): 100times/2s

This endpoint returns a list of accounts owned by this API user.

### HTTP Request

`GET /v1/account/accounts`

### Request Parameters

<aside class="notice">No parameter is available for this endpoint</aside>
> The above command returns JSON structured like this:

```json
  "data": [
    {
      "id": 100009,
      "type": "spot",
      "state": "working"
    }
  ]
```

### Response Content

| Field   | Data Type | Description                                                  | Value Range                                                  |
| ------- | --------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| id      | integer   | Unique account id                                            | NA                                                           |
| state   | string    | Account state                                                | working, lock                                                |
| type    | string    | The type of this account                                     | spot, margin, otc, point, super-margin, investment, borrow   |



## Get Account Balance of a Fund Account

API Key Permission：Read<br>
Rate Limit (NEW): 100times/2s

This endpoint returns the balance of an account specified by account id.

### HTTP Request

`GET /v2/external/account/account/balance`

### Request Parameters


| Field   | Required | Data Type   | Description                                                         | Default | Values |
| ---------- | -------- | ------ | ------------------------------------------------------------ | ------ | -------- |
| types | true     | string | account type，（separate with comma） |        |    asset：fund account      |
| currency | true     | string | currency，（separate with comma） |        |    refer to `GET /v1/account/accounts`      |



### Response Content

| Parameter | Required | Data type | Description     | Values                                                     |
| -------- | -------- | -------- | -------- | ------------------------------------------------------------ |
| code       | false     | integer     | status code  |                                                              |
| data    | true     | object    |  |                              |
| { balance     | true     | string   | available balance | |
| currency     | true     | string   | currency | |
| state     | true     | string   | account status | normal, locked|
| suspense     | true     | string   | frozen account | |
| type }     | true     | string   | account type | asset：fund account|
| message     | false     | string   | error message（eng） | |


## Get Account Balance of a Trading Account

API Key Permission：Read<br>
Rate Limit (NEW): 100times/2s

This endpoint returns the balance of an account specified by account id.

### HTTP Request

`GET /v1/account/accounts/{account-id}/balance`

'account-id': The specified account id to get balance for, can be found by query '/v1/account/accounts' endpoint.



### Response Content

| Field | Data Type | Description                          | Value Range                                                |
| ----- | --------- | ------------------------------------ | ---------------------------------------------------------- |
| id    | integer   | Unique account id                    | NA                                                         |
| state | string    | Account state                        | working, lock                                              |
| type  | string    | The type of this account             | spot |


## Asset Transfer

API Key Permission：Trade<br>

This endpoint allows parent user and sub user to transfer asset between accounts.<br>

Features now supported for both parent user and sub user include: <br>
1.transfer asset between fund account and trading account; <br>


Other transfer functions will be gradually launched later, please take note on API announcement in near future. <br>

### HTTP Request

- POST `/v1/account/transfer`

### Request Parameters

| Parameter         | Required | Data Type | Description                | Values                            |
| ----------------- | -------- | --------- | -------------------------- | --------------------------------- |
| from-user         | true     | long      | Transfer out user uid      | parent user uid, sub user uid     |
| from-account-type | true     | string    | Transfer out account type  | spot, margin                      |
| from-account      | true     | long      | Transfer out account id    |                                   |
| to-user           | true     | long      | Transfer in user uid       | parent user uid, sub user uid     |
| to-account-type   | true     | string    | Transfer in account type   | spot, margin                      |
| to-account        | true     | long      | Transfer in account id     |                                   |
| currency          | true     | string    | Currency name              | Refer to GET /v1/common/currencys |
| amount            | true     | string    | Amount of fund to transfer |                                   |


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

### Response Content
| Field          | Required | Data Type | Description    | Values          |
| -------------- | -------- | --------- | -------------- | --------------- |
| status         | true     | string    | Request status | "ok" or "error" |
| data           | true     | list      |                |                 |
| {transact-id   | true     | int       | Transfer id    |                 |
| transact-time} | true     | long      | Transfer time  |                 |

## Error Code

Below is the error code, error message and description returned by Account APIs.

| Error Code | Error Message                               | Description                                                  |
| ---------- | ------------------------------------------- | ------------------------------------------------------------ |
| 500        | system error                                | Server internal error                                        |
| 1002       | forbidden                                   | Operation is forbidden, such as the account Id and UID doesn't match |
| 2002       | "invalid field value in `currency`"         | Parameter currency is invalid                                |
| 2002       | "invalid field value in `transactTypes`"    | Parameter transactTypes is invalid (should be transfer)      |
| 2002       | "invalid field value in `sort`"             | Parameter sort is invalid (should be 'asc' or 'desc')        |
| 2002       | "value in `fromId` is not found in record”  | Value fromId doesn't exist                                   |
| 2002       | "invalid field value in `accountId`"        | Parameter accountId is invalid (should not be empty)         |
| 2002       | "value in `startTime` exceeded valid range" | Value startTime is later than current time or earlier than 180 days ago |
| 2002       | "value in `endTime` exceeded valid range")  | Value endTime is earlier than startTime, or 10 days later than startTime |

# Wallet (Deposit and Withdraw)

## Introduction

Wallet APIs provide query functionality for deposit address, withdraw address, withdraw quota, deposit and withdraw history, and also provide withdraw and cancel-withdraw functionality.

<aside class="notice">All endpoints in this section require authentication</aside>

## Query Deposit Address

Parent user and sub user could query deposit address of corresponding chain, for a specific crypto currency (except IOTA).

API Key Permission：Read<br>
Rate Limit (NEW): 20times/2s

<aside class="notice"> The endpoint does not support deposit address querying for currency "IOTA" at this moment </aside>

### HTTP Request

`GET v2/account/deposit/address`


### Request Parameters

| Field Name | Data Type | Mandatory | Default Value | Description                                         |
| ---------- | --------- | --------- | ------------- | --------------------------------------------------- |
| currency   | string    | true      | N/A           | Crypto currency,refer to `GET /v1/common/currencys` |

> The above command returns JSON structured like this:

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

### Response Content

| Field Name | Data Type | Description            |
| ---------- | --------- | ---------------------- |
| code       | int       | Status code            |
| message    | string    | Error message (if any) |
| data       | object    |                        |
| { currency | string    | Crypto currency        |
| address    | string    | Deposit address        |
| addressTag | string    | Deposit address tag    |
| chain }    | string    | Block chain name       |



## Query withdraw address

API Key Permission: Read<br>

This endpoint allows parent user to query withdraw address available for API key.<br>

<aside class="notice">To get the withdraw address from API , user needs to add the withdrawal address from the Web UI first</aside>

### HTTP Request

- GET `/v2/account/withdraw/address`

### Request Parameters

| Parameter | Required | Data Type | Description                                                  | Default Value                                                | Value Range                                                  |
| --------- | -------- | --------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| currency  | true     | string    | Crypto currency                                              |                                                              | btc, ltc, bch, eth, etc ...(refer to GET /v1/common/currencys) |
| chain     | false    | string    | Block chain name                                             | When chain is not specified, the reponse would include the records of ALL chains. |                                                              |
| note      | false    | string    | The note of withdraw address                                 | When note is not specified, the reponse would include the records of ALL notes. |                                                              |
| limit     | false    | int       | The number of items to return                                | 100                                                          | [1,500]                                                      |
| fromId    | false    | long      | First record ID in this query (only valid for next page querying; please refer to note) | NA                                                           |                                                              |
> Response:

```json
{
    "code": 200,
    "data": [
        {
            "currency": "usdt",
            "chain": "usdt",
            "note": "huobi",
            "addressTag": "",
            "address": "15PrEcqTJRn4haLeby3gJJebtyf4KgWmSd"
        }
    ]
}
```

### Response Content
| Field Name | Mandatory | Data Type | Description                                                  | Value Range |
| ---------- | --------- | --------- | ------------------------------------------------------------ | ----------- |
| code       | true      | int       | Status code                                                  |             |
| message    | false     | string    | Error message (if any)                                       |             |
| data       | true      | object    |                                                              |             |
| { currency | true      | string    | Crypto currency                                              |             |
| chain      | true      | string    | Block chain name                                             |             |
| note       | true      | string    | The address note                                             |             |
| addressTag | false     | string    | The address tag，if any                                      |             |
| address }  | true      | string    | Withdraw address                                             |             |
| nextId     | false     | long      | First record ID in next page (only valid if exceeded page size) |             |

Note:<br>
Only when the number of items within the query window exceeded the page limitation (defined by “limit”), Huobi Hong Kong server returns “nextId”. Once received “nextId”, API user should –<br>
1) Be aware of that, some items within the query window were not returned due to the page size limitation.<br>
2) In order to get these items from Huobi Hong Kong server, adopt the “nextId” as “fromId” and submit another request, with other request parameters no change.<br>
3) “nextId” and “fromId” are for recurring query purpose and the ID itself does not have any business implication.<br>


## Search for Existed Withdraws and Deposits

API Key Permission：Read<br>
Rate Limit (NEW): 20times/2s

Parent user and sub user search for all existed withdraws and deposits and return their latest status.

### HTTP Request

`GET /v1/query/deposit-withdraw`


### Request Parameters

| Parameter | Data Type | Required | Description                     | Value Range                                      | Default Value                                                |      |
| --------- | --------- | -------- | ------------------------------- | ------------------------------------------------ | ------------------------------------------------------------ | ---- |
| currency  | string    | false    | The crypto currency to withdraw | NA                                               | When currency is not specified, the response would include the records of ALL currencies. |      |
| type      | string    | true     | Define transfer type to search  |       | deposit, withdraw, sub user can only use deposit                                                             |      |
| from      | string    | false    | The transfer id to begin search | 1 ~ latest record ID                             | When 'from' is not specified, the default value would be 1 if 'direct' is 'prev' with the response in ascending order, the default value would be the ID of latest record if 'direct' is 'next' with the response in descending order. |      |
| size      | string    | false    | The number of items to return   | 1-500                                            | 100                                                          |      |
| direct    | string    | false    | the order of response           | 'prev' (ascending), 'next' (descending)          | 'prev'                                                       |      |

> The above command returns JSON structured like this:

```json
{
	"status": "ok",
	"data": [{
		"id": 24383070,
		"type": "deposit",
		"currency": "usdt",
		"chain": "usdterc20",
		"tx-hash": "16382690",
		"amount": 4.000000000000000000,
		"address": "0x138d709030b4e096044d371a27efc5c562889b9b",
		"address-tag": "",
		"fee": 0,
		"state": "safe",
		"created-at": 1571303815800,
		"updated-at": 1571303815826
	}]
}
```

### Response Content

| Field       | Data Type | Description                                                  |
| ----------- | --------- | ------------------------------------------------------------ |
| id          | integer   | Transfer id                                                  |
| type        | string    | Define transfer type to search, possible values: [deposit, withdraw] Sub-user can only put "deposit"|
| currency    | string    | The crypto currency to withdraw                              |
| tx-hash     | string    | The on-chain transaction hash. If this is a "fast withdraw", then it is not on-chain transfer, and this value is empty. |
| chain       | string    | Block chain name                                             |
| amount      | float     | The number of crypto asset transfered in its minimum unit    |
| address     | string    | The deposit or withdraw target address                       |
| address-tag | string    | The user defined address tag                                 |
| fee         | float     | Withdrawal fee                                                 |
| state       | string    | The state of this transfer (see below for details)           |
| error-code  | string    | Error code for withdrawal failure, only returned when the type is "withdraw" and the state is "reject", "wallet-reject" and "failed". |
| error-msg   | string    | Error description of withdrawal failure, only returned when the type is "withdraw" and the state is "reject", "wallet-reject" and "failed". |
| created-at  | integer   | The timestamp in milliseconds for the transfer creation      |
| updated-at  | integer   | The timestamp in milliseconds for the transfer's latest update |

**List of possible deposit state**

| State      | Description                                        |
| ---------- | -------------------------------------------------- |
| unknown    | On-chain transfer has not been received            |
| confirming | On-chain transfer waits for first confirmation     |
| confirmed  | On-chain transfer confirmed for at least one block, user is able to transfer and trade |
| safe | Multiple on-chain confirmed, user is able to withdraw |
| orphan |On-chain transfer confirmed but currently in an orphan branch |

**List of possible withdraw state**

| State           | Description                                       |
| --------------- | ------------------------------------------------- |
| verifying       | Awaiting verification                             |
| failed          | verification failed                               |
| submitted       | Withdraw request submitted successfully           |
| reexamine       | Under examination for withdraw validation         |
| canceled        | Withdraw canceled by user                         |
| pass            | Withdraw validation passed                        |
| reject          | Withdraw validation rejected                      |
| pre-transfer    | Withdraw is about to be released                  |
| wallet-transfer | On-chain transfer initiated                       |
| wallet-reject   | Transfer rejected by chain                        |
| confirmed       | On-chain transfer completed with one confirmation |
| confirm-error   | On-chain transfer faied to get confirmation       |
| repealed        | Withdraw terminated by system                     |

## Error Code

Below is the response code, message and description returned by Wallet APIs.

| Response Code | Message                              | Description            |
| ------------- | ------------------------------------ | ---------------------- |
| 200           | success                              | Successful             |
| 500           | error                                | Server internal error  |
| 1002          | unauthorized                         | User is unautherized   |
| 1003          | invalid signature                    | API signature is wrong |
| 2002          | invalid field value in "field name"  | Parameter is invalid   |
| 2003          | missing mandatory field "field name" | Parameter is missing   |


# Sub user management

## Introduction

Sub user management APIs provide sub user account management (creation, query, permission, transfer), sub user API key management (creation, update, query, deletion), sub user address (deposit, withdraw) query and balance query.

<aside class="notice">All endpoints in this section require authentication</aside>


## Get Sub User's List

Via this endpoint parent user is able to query a full list of sub user's UID as well as their status.

API Key Permission: Read

### HTTP Request

- GET `/v2/sub-user/user-list`

### Request Parameter

| Field  | Mandatory | Data Type | Description                                                  | Default Value | Possible Value |
| ------ | --------- | --------- | ------------------------------------------------------------ | ------------- | -------------- |
| fromId | FALSE     | long      | First record ID in next page (only valid if exceeded page size) |               |                |

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

### Response

| Field       | Mandatory | Data Type | Description                                                  | Possible Value |
| ----------- | --------- | --------- | ------------------------------------------------------------ | -------------- |
| code        | TRUE      | int       | Status code                                                  |                |
| message     | FALSE     | string    | Error message (if any)                                       |                |
| data        | TRUE      | object    | In ascending order of uid, each response contains maximum 100 records |                |
| { uid       | TRUE      | long      | Sub user’s UID                                               |                |
| userState } | TRUE      | string    | Sub user’s status                                            | lock, normal   |
| nextId      | FALSE     | long      | First record ID in next page (only valid if exceeded page size) |                |

## Lock/Unlock Sub User

API Key Permission：Trade<br>
Rate Limit (NEW): 20times/2s

This endpoint allows parent user to lock or unlock a specific sub user.

### HTTP Request

`POST /v2/sub-user/management`

### Request Parameters

| Parameter  | Data Type | Required | Description                                       | Value Range
| ---------  | --------- | -------- | -----------                                       | -----------
| subUid    | long  | true     | Sub user UID | NA
| action   | string    | true     | Action type                   | lock,unlock 

> The above command returns JSON structured like this:

```json
{
  "code": 200,
	"data": {
     "subUid": 12902150,
     "userState":"lock"}
}
```

### Response Content

| Field               | Data Type | Description                           | Value Range
| ---------           | --------- | -----------                           | -----------
| subUid                 | long   | sub user UID                     | NA
| userState                | string    | The state of sub user             | lock,normal

## Get Sub User's Status

Via this endpoint, parent user is able to query sub user's status by specifying a UID.

API Key Permission: Read

### HTTP Request

- GET `/v2/sub-user/user-state`

### Request Parameter

| Field  | Mandatory | Data Type | Description    | Default Value | Possible Value |
| ------ | --------- | --------- | -------------- | ------------- | -------------- |
| subUid | TRUE      | long      | Sub user's UID |               |                |

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

### Response

| Field       | Mandatory | Data Type | Description            | Possible Value |
| ----------- | --------- | --------- | ---------------------- | -------------- |
| code        | TRUE      | int       | Status code            |                |
| message     | FALSE     | string    | Error message (if any) |                |
| data        | TRUE      | object    |                        |                |
| { uid       | TRUE      | long      | Sub user’s UID         |                |
| userState } | TRUE      | string    | Sub user’s status      | lock, normal   |

## Set Tradable Market for Sub Users

API Key Permission: Trade

Parent user is able to set tradable market for a batch of sub users through this endpoint.
By default, sub user’s trading permission in spot market is activated.

###HTTP Request

- POST `/v2/sub-user/tradable-market`

### Request Parameters

| Parameter   | Mandatory | Data Type | Length | Description                                               | Possible Value               |
| ----------- | --------- | --------- | ------ | --------------------------------------------------------- | ---------------------------- |
| subUids     | true      | string    | -      | Sub user's UID list (maximum 50 UIDs, separated by comma) | -                            |
| accountType | true      | string    | -      | Account type                                              | spot |
| activation  | true      | string    | -      | Account activation                                        | activated,deactivated        |

> Response:

```json
{
    "code": 200,
    "data": [
        {
            "subUid": "132208121",
            "accountType": "isolated-margin",
            "activation": "activated"
        }
    ]
}
```

### Response Content

| Parameter   | Mandatory | Data Type | Length | Description                                                  | Possible Value               |
| ----------- | --------- | --------- | ------ | ------------------------------------------------------------ | ---------------------------- |
| code        | true      | int       | -      | Status code                                                  |                              |
| message     | false     | string    | -      | Error message (if any)                                       |                              |
| data        | true      | object    |        |                                                              |                              |
| {subUid     | true      | string    | -      | Sub user's UID                                               | -                            |
| accountType | true      | string    | -      | Account type                                                 | spot |
| activation  | true      | string    | -      | Account activation                                           | activated,deactivated        |
| errCode     | false     | int       | -      | Error code in case of rejection (only valid when the requested UID being rejected) |                              |
| errMessage} | false     | string    | -      | Error message in case of rejection (only valid when the requested UID being rejected) |                              |

## Get Sub User's Account List

Via this endpoint parent user is able to query account list of sub user by specifying a UID.

API Key Permission: Read

### HTTP Request

- GET `/v2/sub-user/account-list`

### Request Parameter

| Field  | Mandatory | Data Type | Description    | Default Value | Possible Value |
| ------ | --------- | --------- | -------------- | ------------- | -------------- |
| subUid | TRUE      | long      | Sub User's UID |               |                |



### Response

| Field             | Mandatory | Data Type | Description                                                  | Possible Value                                    |
| ----------------- | --------- | --------- | ------------------------------------------------------------ | ------------------------------------------------- |
| code              | TRUE      | int       | Status code                                                  |                                                   |
| message           | FALSE     | string    | Error message (if any)                                       |                                                   |
| data              | TRUE      | object    |                                                              |                                                   |
| { uid             | TRUE      | long      | Sub user’s UID                                               |                                                   |
| deductMode        | TRUE      | string    | deduct mode                                                  |                                                   |
| list              | TRUE      | object    |                                                              |                                                   |
| { accountType     | TRUE      | string    | Account type                                                 | spot |
| activation        | TRUE      | string    | Account’s activation                                         | activated, deactivated                            |
| transferrable     | FALSE     | bool      | Transfer permission (only valid for accountType=spot)        | true, false                                       |
| accountIds        | FALSE     | object    |                                                              |                                                   |
| { accountId       | TRUE      | string    | Account ID                                                   |                                                   |
| subType           | FALSE     | string    | Account sub type (only valid for accountType=isolated-margin) |                                                   |
| accountStatus }}} | TRUE      | string    | Account status                                               | normal, locked                                    |



## Get the Aggregated Balance of all Sub-users

API Key Permission：Read<br>
Rate Limit (NEW): 2times/2s

This endpoint returns the aggregated balance from all the sub-users.

### HTTP Request

`GET /v1/subuser/aggregate-balance`



> The above command returns JSON structured like this:

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

### Request Parameters

No parameter is needed for this endpoint
### Response Content

The returned "data" object is a list of aggregated balances
| Field    | Data Type | Description                                                  |
| -------- | --------- | ------------------------------------------------------------ |
| currency | string    | The currency of this balance                                 |
| type     | string    | account type (spot, margin, point,super-margin)              |
| balance  | string    | The total balance in the main currency unit including all balance and frozen banlance |


## Get Account Balance of a Sub-User

API Key Permission：Read<br>
Rate Limit (NEW): 20times/2s

This endpoint returns the balance of a sub-user specified by sub-uid.

### HTTP Request

`GET /v1/account/accounts/{sub-uid}`

'sub-uid': The specified sub user id to get balance for.


### Request Parameters

|Field Name	|Data Type	|Mandatory	|Description												|
|-------		|-------		|-------		|-------													|
| subUid	| long		| ture		| Sub user UID												|

> The above command returns JSON structured like this:

```json
"data": [
  {
    "id": 9910049,
    "type": "spot",
    "list": [
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
```

### Response Content

The returned "data" object is a list of accounts under this sub-user
| Field | Data Type | Description                          | Value Range                           |
| ----- | --------- | ------------------------------------ | ------------------------------------- |
| id    | integer   | Sub account's UID                 | NA                                    |
| type  | string    | The type of this account             | spot |
| list  | object    | The balance details of each currency | NA                                    |

**Per list item content**

| Field    | Data Type | Description                           | Value Range   |
| -------- | --------- | ------------------------------------- | ------------- |
| currency | string    | The currency of this balance          | NA            |
| type     | string    | The balance type                      | trade, frozen |
| balance  | string    | The balance in the main currency unit | NA            |

## Error Code

Below is the error code, error message and description returned by Sub user management APIs

| Error Code | Error Message                                             | Description                                                  |
| ---------- | --------------------------------------------------------- | ------------------------------------------------------------ |
| 1002       | forbidden                                                 | Operation is forbidden, such as sub user creation is not allowed for current user |
| 1003       | unauthorized                                              | Signature is wrong                                           |
| 2002       | invalid field value                                       | Parameter is invalid                                         |
| 2014       | number of sub account in the request exceeded valid range | number of sub account exceeded                               |
| 2014       | number of api key in the request exceeded valid range     | number of API Key exceeded                                   |
| 2016       | invalid request while value specified in sub user states  | lock or unlock failure                                       |

# Trading

## Introduction

Trading APIs provide trading related functionality, including placing order, canceling order, order history query, trading history query, transaction fee query.

<aside class="notice">All endpoints in this section require authentication</aside>
<aside class="warning">The parameter "account-id" and "source" should be set properly, refer to details in Request Parameters description below.</aside>

Below is the glossary of trading related field:

**order type**: The order type is consist of trade direction and behavior type: [direction]-[type]

direction:

- buy
- sell

type:

- market : The price is not required in order creation request, you only need to specify either volume or amount. The matching and trade will happen automatically according to the request.
- limit: Both of the price and amount should be specified in order creation request.
- limit-maker: The price is specified in order creation request as market maker. It will not be matched in the matching queue.
- ioc: ioc stands for "immediately or cancel", it means the order will be canceled if it couldn't be matched. If the order is partially traded, the remaining part will be canceled.
- limit-fok: fok stands for "fill or kill", it means the order will be cancelled if it couldn't be **fully** matched. Even if the order can be partially filled, the entire order will be cancelled.
- market-grid: Grid trading market order (not supported by API)
- limit-grid: Grid trading limit order (not supported by API)
- stop-limit: The price in order creation request is the trigger price. The order will be put into matching queue only when the market price reaches the trigger price. This type is replaced by conditional order, please use conditional order APIs

**order source**: the origin of the order

- spot-api: API order from spot account

**order state**:

- created: The order is created, and not in the matching queue yet.
- submitted: The order is submitted, and already in the matching queue, waiting for deal.
- partial-filled: The order is already in the matching queue and partially traded, and is waiting for further matching and trade.
- filled: The order is already traded and not in the matching queue any more.
- partial-canceled: The order is not in the matching queue any more. The status is transferred from 'partial-filled', the order is partially trade, but remaining is canceled.
- canceling: The order is under canceling, but haven't been removed from matching queue yet.
- canceled: The order is not in the matching queue any more, and completely canceled. There is no trade associated with this order.

**IDs**: The frequently used identities are listed below:

- order-id: The unique identity for order.
- client-order-id: The identity defined by the client. This id is included in order creation request, and will be returned as order-id. For completed orders, clientOrderId will be valid for 2 hours since the order creation (it is still valid for 8 hours concerning other orders). That is to say, if an order has been created for more than 2 hours, clientOrderId can’t be used to query the completed order (It is recommended to check it with orderid). Among them, the status of the completed order includes partially canceled, canceled, and fully executed. The allowed characters are letters (case sensitive), digit, underscore (_) and hyphen (-), no more than 64 chars.
- match-id : The identity for order matching.
- trade-id : The unique identity for the trade.

## Place a New Order

API Key Permission：Trade<br>
Rate Limit (NEW): 100times/2s

This endpoint places a new order and sends to the exchange to be matched.

### HTTP Request

`POST /v1/order/orders/place`

### Request Parameters

| Parameter       | Data Type | Required | Default  | Description                                                  | Value Range                                                  |
| --------------- | --------- | -------- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| account-id      | string    | true     | NA       | The account id used for this trade                           | Refer to `GET /v1/account/accounts`                          |
| symbol          | string    | true     | NA       | The trading symbol to trade                                  | Refer to `GET /v1/common/symbols`                            |
| type            | string    | true     | NA       | The order type                                               | buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker, buy-stop-limit, sell-stop-limit, buy-limit-fok, sell-limit-fok, buy-stop-limit-fok, sell-stop-limit-fok |
| amount          | string    | true     | NA       | order size (for buy market order, it's order value)          | NA                                                           |
| price           | string    | false    | NA       | The order price (not available for market order)             | NA                                                           |
| source          | string    | false    | spot-api | When trade with spot use 'spot-api';When trade with isolated margin use 'margin-api'; When trade with cross margin use 'super-margin-api';When trade with c2c-margin use 'c2c-margin-api'; | api, margin-api,super-margin-api,c2c-margin-api              |
| client-order-id | string    | false    | NA       | Client order ID (maximum 64-character length, to be unique within 8 hours) |                                                              |
| stop-price      | string    | false    | NA       | Trigger price of stop limit order                            |                                                              |
| operator        | string    | false    | NA       | operation charactor of stop price                            | gte – greater than and equal (>=), lte – less than and equal (<=) |

> The above command returns JSON structured like this:

```json
  "data": "59378"
```

### Response Content

<aside class="notice">The returned data object is a single string which represents the order id</aside>
If client order ID duplicates with a previous order (within 8 hours), the endpoint reverts error message `invalid.client.order.id`.

**buy-limit-maker**

If the order price is greater than or equal to the lowest selling price in the market, the order will be rejected.

If the order price is less than the lowest selling price in the market, the order will be accepted.

**sell-limit-maker**

If the order price is less than or equal to the highest buy price in the market, the order will be rejected.

If the order price is greater than the highest buy price in the market, the order will be accepted.


## Place a Batch of Orders

API Key Permission：Trade<br>
Rate Limit (NEW): 50times/2s

A batch contains at most 10 orders.

### HTTP Request

- POST ` /v1/order/batch-orders`

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

### Request Parameters

| Parameter       | Data Type | Required | Default  | Description                                                  |
| --------------- | --------- | -------- | -------- | ------------------------------------------------------------ |
| [{ account-id   | string    | true     | NA       | The account id, refer to `GET /v1/account/accounts`. Use 'spot' `account-id` for spot trading, use 'margin' `account-id` for isolated margin trading, use ‘super-margin’  `account-id` for cross margin trading. use borrow account id for c2c margin trading |
| symbol          | string    | true     | NA       | The trading symbol, i.e. btcusdt, ethbtc...(Refer to `GET /v1/common/symbols`) |
| type            | string    | true     | NA       | The type of order, including 'buy-market', 'sell-market', 'buy-limit', 'sell-limit', 'buy-ioc', 'sell-ioc', 'buy-limit-maker', 'sell-limit-maker' (refer to detail below), 'buy-stop-limit', 'sell-stop-limit', buy-limit-fok, sell-limit-fok, buy-stop-limit-fok, sell-stop-limit-fok. |
| amount          | string    | true     | NA       | The order size (for buy market order, it's order value)      |
| price           | string    | false    | NA       | The order price (not available for market order)             |
| source          | string    | false    | spot-api | When trade with spot use 'spot-api';When trade with margin use 'margin-api'; When trade with super-margin use 'super-margin-api';When trade with c2c-margin use 'c2c-margin-api' |
| client-order-id | string    | false    | NA       | Client order ID (maximum 64-character length)                |
| stop-price      | string    | false    | NA       | Trigger price of stop limit order                            |
| operator}]      | string    | false    | NA       | Operation character of stop price, use 'gte' for greater than and equal (>=), use 'lte' for less than and equal (<=) |

**buy-limit-maker**

If the order price is greater than or equal to the lowest selling price in the market, the order will be rejected.

If the order price is less than the lowest selling price in the market, the order will be accepted.

**sell-limit-maker**

If the order price is less than or equal to the highest buy price in the market, the order will be rejected.

If the order price is greater than the highest buy price in the market, the order will be accepted.

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

### Response Content

| Field           | Data Type | Description                                 |
| --------------- | --------- | ------------------------------------------- |
| [{order-id      | integer   | The order id                                |
| client-order-id | string    | The client order id (if available)          |
| err-code        | string    | The error code (only for rejected order)    |
| err-msg}]       | string    | The error message (only for rejected order) |

If client order ID duplicates with a previous order , the endpoint responds that previous order's Id and client order ID.

## Submit Cancel for an Order

API Key Permission：Trade<br>
Rate Limit (NEW): 100times/2s

This endpoint submits a request to cancel an order.

<aside class="warning">The actual result of the cancellation request needs to be checked by order status or match result endpoints after submitting the request.</aside>
### HTTP Request

`POST /v1/order/orders/{order-id}/submitcancel`

'order-id': the previously returned order id when order was created



### Request Parameters

| Parameter       | Data Type | Required | Default | Description                                                  |
| --------------- | --------- | -------- | ------- | ------------------------------------------------------------ |
| order-id | string    | true     | NA      | order id which needs to be filled in the path |
| symbol | string    | false     | NA      | symbol which needs to be filled in the URL |

> The above command returns JSON structured like this:

```json
  "data": "59378"
```

### Response Content

<aside class="notice">The returned data object is a single string which represents the order id</aside>
### Error Code

> Response:

```json
{
  "status": "error",
  "err-code": "order-orderstate-error",
  "err-msg": "Incorrect order state",
  "order-state":-1 // current order state
}
```

The possible values of "order-state" includes -

| order-state | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| -1          | order was already closed in the long past (order state = cancelled, partially-cancelled, filled, partially-filled) |
| 1           | created                                             |
| 3           | submitted                                           |
| 4           | partial-filled                                      |
| 5           | partially-cancelled                                             |
| 6           | filled                                                       |
| 7           | cancelled                                                     |
| 10          | cancelling                                                   |

## Submit Cancel for an Order (based on client order ID)

API Key Permission：Trade<br>
Rate Limit (NEW): 100times/2s

This endpoint submit a request to cancel an order based on client-order-id .

<aside class="notice">It is suggested to use /v1/order/orders/{order-id}/submitcancel to cancel a single order, which is faster and more stable</aside>
<aside class="warning">This only submits the cancel request, the actual result of the canel request needs to be checked by order status or match result endpoints</aside>

### HTTP Request

`POST /v1/order/orders/submitCancelClientOrder`


### Request Parameters

| Parameter       | Data Type | Required | Default | Description                                                  |
| --------------- | --------- | -------- | ------- | ------------------------------------------------------------ |
| client-order-id | string    | true     | NA      | Client order ID, it must exist within 8 hours, otherwise it is not allowed to use when placing a new order |

> The above command returns JSON structured like this:

```json
  "data": "59378"
```

### Response Content

| Field | Data Type | Description              |
| ----- | --------- | ------------------------ |
| data  | int       | Cancellation status code |

| Status Code | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| -1          | order was already closed in the long past (order state = cancelled, partially-cancelled, filled, partially-filled) |
| 0           | client-order-id not found                                    |
| 1           | created                                             |
| 3           | submitted                                           |
| 4           | partial-filled                                      |
| 5           | partially-cancelled                                             |
| 6           | filled                                                       |
| 7           | cancelled                                                     |
| 10          | cancelling                                                   |


## Get All Open Orders

API Key Permission：Read<br>
Rate Limit (NEW): 50times/2s

This endpoint returns all open orders which have not been filled completely.

### HTTP Request

`GET v1/order/openOrders`


### Request Parameters

| Parameter  | Data Type | Required                                                     | Default | Description                                | Value Range                                                  |
| ---------- | --------- | ------------------------------------------------------------ | ------- | ------------------------------------------ | ------------------------------------------------------------ |
| account-id | string    | true                                                         | NA      | The account id used for this trade         | Refer to `GET /v1/account/accounts`                          |
| symbol     | string    | true                                                         | NA      | The trading symbol to trade                | Refer to `GET /v1/common/symbols`                            |
| side       | string    | false                                                        | NA      | Filter on the direction of the trade       | buy, sell                                                    |
| from       | string    | false                                                        | NA      | start order ID the searching to begin with |                                                              |
| direct     | string    | false (if field "from" is defined, this field "direct" becomes mandatory) | NA      | searching direction                        | prev - in ascending order from the start order ID; next - in descending order from the start order ID |
| size       | int       | false                                                        | 100     | The number of orders to return             | [1, 500]                                                     |

> The above command returns JSON structured like this:

```json
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
```

### Response Content

| Field              | Data Type | Description                                                  |
| ------------------ | --------- | ------------------------------------------------------------ |
| id                 | integer   | Order id                                                     |
| client-order-id    | string    | Client order id, can be returned from all open orders (if specified). |
| symbol             | string    | The trading symbol to trade, e.g. btcusdt, bccbtc            |
| price              | string    | The limit price of limit order                               |
| created-at         | int       | The timestamp in milliseconds when the order was created     |
| type               | string    | All possible order type (refer to introduction in this section) |
| filled-amount      | string    | The amount which has been filled                             |
| filled-cash-amount | string    | The filled total in quote currency                           |
| filled-fees        | string    | Transaction fee (Accurate fees refer to matchresults endpoint) |
| source             | string    | The source where the order was triggered, possible values: sys, web, api, app |
| state              | string    | Order status, valid values: created, submitted, partial-filled |
| stop-price         | string    | false                                                        |
| operator           | string    | false                                                        |

## Submit Cancel for Multiple Orders by Criteria

API Key Permission：Trade<br>
Rate Limit (NEW): 50times/2s

This endpoint submit cancellation for multiple orders (not exceeding 100 orders per request) at once with given criteria.

This endpoint only submit the cancellation request, the actual cancellation result will need to be confirmed by other endpoints 
like order status, matchresult, etc.

### HTTP Request

`POST /v1/order/orders/batchCancelOpenOrders`


| Parameter  | Data Type | Required | Default | Description                                                  | Value Range                                                  |
| ---------- | --------- | -------- | ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| account-id | string    | false    | NA      | The account id used for this cancel                          | Refer to `GET /v1/account/accounts`                          |
| symbol     | string    | false    | all     | The trading symbol list (maximum 10 symbols, separated by comma, default value all symbols) | All supported trading symbol, e.g. btcusdt, bccbtc.Refer to `GET /v1/common/symbols` |
| types      | string    | false    | NA      | One or more types of order to include in the search, use comma to separate. | buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-stop-limit, sell-stop-limit, buy-limit-fok, sell-limit-fok, buy-stop-limit-fok, sell-stop-limit-fok |
| side       | string    | false    | NA      | Filter on the direction of the trade                         | buy, sell                                                    |
| size       | int       | false    | 100     | The number of orders to cancel                               | [1, 100]                                                     |

> The above command returns JSON structured like this:

```json
  "data": {
    "success-count": 2,
    "failed-count": 0,
    "next-id": 5454600
  }
```

### Response Content

| Field         | Data Type | Description                                                  |
| ------------- | --------- | ------------------------------------------------------------ |
| success-count | integer   | The number of cancel request sent successfully               |
| failed-count  | integer   | The number of cancel request failed                          |
| next-id       | integer   | the next order id that can be cancelled, -1 indicates no open orders |

## Submit Cancel for Multiple Orders by IDs

API Key Permission：Trade<br>
Rate Limit (NEW): 50times/2s

This endpoint submit cancellation for multiple orders at once with given ids. It is suggested to use order-ids instead of 
client-order-ids, so that the cancellation is faster, more accurate and more stable. 

### HTTP Request

`POST /v1/order/orders/batchcancel`


### Request Parameters

| Parameter        | Data Type | Required | Description                                                  | Value Range         |
| ---------------- | --------- | -------- | ------------------------------------------------------------ | ------------------- |
| order-ids        | string[]  | false    | The order ids to cancel (Either order-ids or client-order-ids can be filled in one batch request). It is suggest to use order-ids rather than client-order-ids, the former is faster and more stable | No more than 50 orders per request|
| client-order-ids | string[]  | false    | The client order ids to cancel (Either order-ids or client-order-ids can be filled in one batch request), it must exist already, otherwise it is not allowed to use when placing a new order | No more than 50 orders per request|

> The above command returns JSON structured like this:

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

### Response Content

| Field    | Data Type | Description                                                  |
| -------- | --------- | ------------------------------------------------------------ |
| {success | string[]  | Cancelled order list (Can be order ID list or client order list, based on the request) |
| failed}  | string[]  | Failed order list (Can be order ID list or client order list, based on the request) |

The failed id list has below fields

| Fields          | Data Type | Description                                                  |
| --------------- | --------- | ------------------------------------------------------------ |
| [{ order-id     | string    | The order id (if the request is based on order-ids)          |
| client-order-id | string    | The client order id (if the request is based on client-order-ids) |
| err-code        | string    | The error code (only applicable for rejected order)          |
| err-msg         | string    | The error message (only applicable for rejected order)       |
| order-state }]  | string    | Current order state (if available)                           |

The possible values of "order-state" includes -

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



##  Dead man’s switch （Cancel on Disconnect）

API Key Permission：Trade<br>

The Dead man’s switch  protects the user’s assets when the connection to the exchange is lost due to network or system errors. <br>
Turn on/off the Dead man’s switch. If the Dead man’s switch is turned on and the API call isn’t sent twice within the set time, the platform will cancel all of your orders on the spot market（a maximum cancellation of 500 orders）.

### HTTP Request

- POST `/v2/algo-orders/cancel-all-after`

> Request:

```json
{
  "timeout": "10"
}
```

### Request Parameters

| Parameter  | Data Type | Required | Default | Description                                                  | Value Range                                                  |
| -------- | -------- | ---- | ------------------------------------ | ------ | ---------------- |
| timeout  | true     | int  | time out duration (unit：second); see notes for details | NA     | 0 or >=5 seconds |


> Turn On Successful 
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


> Turn Off Successful 
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


> Turn On/Off Failed
> Response:

```json
{
"code": 2003,
"message": "missing mandatory field"
}
```

### Response Content

| **Name**      | **Mandatory** | **Type** | **Description**            |
| ------------- | ------------- | -------- | -------------------------- |
| code          | true          | int      | status code                |
| message       | false         | string   | error description (if any) |
| data          | true          | object   |                            |
| { currentTime | true          | long     | current time               |
| triggerTime } | true          | long     | trigger time               |


## 

## Get the Order Detail of an Order

API Key Permission：Read<br>
Rate Limit (NEW): 50times/2s

This endpoint returns the detail of a specific order. If an order is created via API, then it's no longer queryable after being cancelled for 2 hours.

### HTTP Request

`GET /v1/order/orders/{order-id}`

### Request Parameters
| **Name**      | **Mandatory** | **Type** | **Description**            |
| ------------- | ------------- | -------- | -------------------------- |
| order-id      | true          | string   | order id when order was created. Place it within path|



> The above command returns JSON structured like this:

```json
{  
  "data": {
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

### Response Content

| Field              | Data Type | Description                                                  |
| ------------------ | --------- | ------------------------------------------------------------ |
| id                 | integer   | order id                                                     |
| client-order-id    | string    | Client order id ("client-order-id" (if specified) can be returned from all open orders.	"client-order-id"  (if specified) can be returned only from closed orders (state <> canceled) created within 7 days.	"client-order-id"  (if specified) can be returned only from closed orders (state = canceled) created within 8 hours.) |
| symbol             | string    | The trading symbol to trade, e.g. btcusdt, bccbtc            |
| account-id         | string    | The account id which this order belongs to                   |
| amount             | string    | The amount of base currency in this order                    |
| price              | string    | The limit price of limit order                               |
| created-at         | int       | The timestamp in milliseconds when the order was created     |
| finished-at        | int       | The timestamp in milliseconds when the order was changed to a final state. This is not the time the order is matched. |
| canceled-at        | int       | The timestamp in milliseconds when the order was canceled, if not canceled then has value of 0 |
| type               | string    | All possible order type (refer to introduction in this section) |
| filled-amount      | string    | The amount which has been filled                             |
| filled-cash-amount | string    | The filled total in quote currency                           |
| filled-fees        | string    | Transaction fee (Accurate fees refer to matchresults endpoint) |
| source             | string    | All possible order source (refer to introduction in this section) |
| state              | string    | All possible order state (refer to introduction in this section) |
| stop-price         | string    | trigger price of stop limit order                            |
| operator           | string    | operation character of stop price: gte, lte                  |



## Get the Order Detail of an Order (based on client order ID)

API Key Permission：Read<br>
Rate Limit (NEW):50times/2s

This endpoint returns the detail of one order by specified client order id (within 8 hours). The order created via API will no longer be queryable after being cancelled for more than 2 hours. It is suggested to cancel orders via `GET /v1/order/orders/{order-id}`, which is faster and more stable.

### HTTP Request

`GET /v1/order/orders/getClientOrder`

### Request Parameters

| Parameter     | Data Type | Required | Default | Description     |
| ------------- | --------- | -------- | ------- | --------------- |
| order-id      | string    | true     | NA      | Client order ID |

> The above command returns JSON structured like this:

```json
{  
  "data": {
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

### Response Content

| Field              | Data Type | Description                                                  |
| ------------------ | --------- | ------------------------------------------------------------ |
| id                 | integer   | order id                                                     |
| client-order-id    | string    | Client order id (only those orders created within 8 hours can be returned.) |
| symbol             | string    | The trading symbol to trade, e.g. btcusdt, bccbtc            |
| account-id         | string    | The account id which this order belongs to                   |
| amount             | string    | The amount of base currency in this order                    |
| price              | string    | The limit price of limit order                               |
| created-at         | int       | The timestamp in milliseconds when the order was created     |
| finished-at        | int       | The timestamp in milliseconds when the order was changed to a final state. This is not the time the order is matched. |
| canceled-at        | int       | The timestamp in milliseconds when the order was canceled, if not canceled then has value of 0 |
| type               | string    | All possible order type (refer to introduction in this section) |
| filled-amount      | string    | The amount which has been filled                             |
| filled-cash-amount | string    | The filled total in quote currency                           |
| filled-fees        | string    | Transaction fee (Accurate fees refer to matchresults endpoint) |
| source             | string    | All possible order source (refer to introduction in this section) |
| state              | string    | All possible order state (refer to introduction in this section) |
| stop-price         | string    | trigger price of stop limit order                            |
| operator           | string    | operation character of stop price                            |

If the client order ID is not found, following error message will be returned:
{
    "status": "error",
    "err-code": "base-record-invalid",
    "err-msg": "record invalid",
    "data": null
}



## Get the Match Result of an Order

API Key Permission：Read<br>
Rate Limit (NEW): 50times/2s

This endpoint returns the match result of an order.

### HTTP Request

`GET /v1/order/orders/{order-id}/matchresults`

### Request Parameters

| Parameter     | Data Type | Required | Default | Description     |
| ------------- | --------- | -------- | ------- | --------------- |
| order-id      | string    | true     | NA      | Order ID, place it into path |


> The above command returns JSON structured like this:

```json
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
      "role": maker,
      "filled-points": "0.0",
      "fee-deduct-currency": "",
      "fee-deduct-state": "done"
    }
  ]
```

### Response Content

<aside class="notice">The return data contains a list and each item in the list represents a match result</aside>
| Parameter           | Data Type | Description                                                  |
| ------------------- | --------- | ------------------------------------------------------------ |
| id                  | long      | Internal id                                                  |
| symbol              | string    | The trading symbol to trade, e.g. btcusdt, bccbtc            |
| order-id            | long      | The order id of this order                                   |
| match-id            | long      | The match id of this match                                   |
| trade-id            | integer   | Unique trade ID (NEW)                                        |
| price               | string    | The limit price of limit order                               |
| created-at          | int       | The timestamp in milliseconds when this record is created (slightly later than trade time) |
| type                | string    | All possible order type (refer to introduction in this section) |
| filled-amount       | string    | The amount which has been filled                             |
| filled-fees         | string    | Transaction fee (positive value). If maker rebate applicable, revert maker rebate value per trade (negative value). |
| fee-currency        | string    | Currency of transaction fee or transaction fee rebate (transaction fee of buy order is based on base currency, transaction fee of sell order is based on quote currency; transaction fee rebate of buy order is based on quote currency, transaction fee rebate of sell order is based on base currency) |
| source              | string    | All possible order source (refer to introduction in this section) |
| role                | string    | the role in the transaction: taker or maker                  |
| filled-points       | string    | deduction amount (unit: in ht or hbpoint)                    |
| fee-deduct-currency | string    | deduction type. if blank, the transaction fee is based on original currency; if showing value as "ht", the transaction fee is deducted by HT; if showing value as "hbpoint", the transaction fee is deducted by HB point. |
| fee-deduct-state | string    | Fee deduction status，In deduction：ongoing，Deduction completed：done |

Notes:<br>

- The calculated maker rebate value inside ‘filled-fees’ would not be paid immediately.<br>


## Search Past Orders

API Key Permission：Read<br>
Rate Limit (NEW): 50times/2s

This endpoint returns orders based on a specific searching criteria. The order created via API will no longer be queryable after being cancelled for more than 2 hours.


- Upon user defined “start-time” AND/OR “end-time”, Huobi Hong Kong server will return historical orders whose order creation time falling into the period. The maximum query window between “start-time” and “end-time” is 48-hour. Oldest order searchable should be within recent 180 days. If either “start-time” or “end-time” is defined, Huobi Hong Kong server will ignore “start-date” and “end-date” regardless they were filled or not.

- If user does neither define “start-time” nor “end-time”, but “start-date”/”end-date”, the order searching will be based on defined “date range”, as usual. The maximum query window is 2 days, and oldest order searchable should be within recent 180 days.

- If user does not define any of “start-time”/”end-time”/”start-date”/”end-date”, by default Huobi Hong Kong server will treat current time as “end-time”, and then return historical orders within recent 48 hours.

Huobi Hong Kong Global suggests API users to search historical orders based on “time” filter instead of “date”. In the near future, Huobi Hong Kong Global would remove “start-date”/”end-date” fields from the endpoint, through another notification.


### HTTP Request

`GET /v1/order/orders`


> Request
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


### Request Parameters

| Parameter  | Data Type | Required | Default | Description                                                  | Value Range                                                  |
| ---------- | --------- | -------- | ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| symbol     | string    | true     | NA      | The trading symbol                                           | All supported trading symbols, e.g. btcusdt, bccbtc          |
| types      | string    | false    | NA      | One or more types of order to include in the search, use comma to separate. | buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-stop-limit, sell-stop-limit, buy-limit-fok, sell-limit-fok, buy-stop-limit-fok, sell-stop-limit-fok |
| start-time | long      | false    | -48h    | Search starts time, UTC time in millisecond                  | Value range [((end-time) – 48h), (end-time)], maximum query window size is 48 hours, query window shift should be within past 180 days, query window shift should be within past 2 hours for cancelled order (state = "canceled") |
| end-time   | long      | false    | present | Search ends time, UTC time in millisecond                    | Value range [(present-179d), present], maximum query window size is 48 hours, query window shift should be within past 180 days, queriable range should be within past 2 hours for cancelled order (state = "canceled") |
| states     | string    | true     | NA      | One or more  states of order to include in the search, use comma to separate. | All possible order state (refer to introduction in this section) |
| from       | string    | false    | NA      | Search order id to begin with                                | NA                                                           |
| direct     | string    | false    | both    | Search direction when 'from' is used                         | next, prev                                                   |
| size       | integer   | false    | 100     | The number of orders to return                               | [1, 100]                                                     |

> The above command returns JSON structured like this:

```json
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
  ]
```

### Response Content

| Field              | Data Type | Description                                                  |
| ------------------ | --------- | ------------------------------------------------------------ |
| id                 | long      | Order id                                                     |
| client-order-id    | string    | Client order id ("client-order-id" (if specified) can be returned from all open orders.	"client-order-id" (if specified) can be returned only from closed orders (state <> canceled) created within 7 days.	only those closed orders (state = canceled) created within 8 hours can be returned.) |
| account-id         | long      | Account id                                                   |
| user-id            | integer   | User id                                                      |
| amount             | string    | The amount of base currency in this order                    |
| symbol             | string    | The trading symbol to trade, e.g. btcusdt, bccbtc            |
| price              | string    | The limit price of limit order                               |
| created-at         | long      | The timestamp in milliseconds when the order was created     |
| canceled-at        | long      | The timestamp in milliseconds when the order was canceled, or 0 if not canceled |
| finished-at        | long      | The timestamp in milliseconds when the order was finished, or 0 if not finished |
| type               | string    | All possible order type (refer to introduction in this section) |
| filled-amount      | string    | The amount which has been filled                             |
| filled-cash-amount | string    | The filled total in quote currency                           |
| filled-fees        | string    | Transaction fee (Accurate fees refer to matchresults endpoint) |
| source             | string    | All possible order source (refer to introduction in this section) |
| state              | string    | All possible order state (refer to introduction in this section) |
| exchange           | string    | Internal data                                                |
| batch              | string    | Internal data                                                |
| stop-price         | string    | trigger price of stop limit order                            |
| operator           | string    | operation character of stop price                            |

### Error code for invalid start-date/end-date

| err-code           | scenarios                                                    |
| ------------------ | ------------------------------------------------------------ |
| invalid_interval   | Start date is later than end date; the date between start date and end date is greater than 2 days |
| invalid_start_date | Start date is a future date; or start date is earlier than 180 days ago. |
| invalid_end_date   | end date is a future date; or end date is earlier than 180 days ago. |




## Search Match Results

API Key Permission：Read<br>
Rate Limit (NEW): 20times/2s

This endpoint returns the match results of past and current filled, or partially filled orders based on specific search criteria.

### HTTP Request

`GET /v1/order/matchresults`


### Request Parameters

| Parameter  | Data Type | Required | Default                                                      | Description                                                  | Value Range                                                  |
| ---------- | --------- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| symbol     | string    | true     | N/A                                                          | The trading symbol to trade                                  | All supported trading symbol, e.g. btcusdt, bccbtc.Refer to `GET /v1/common/symbols` |
| types      | string    | false    | all                                                          | The types of order to include in the search                  | buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker, buy-stop-limit, sell-stop-limit |
| start-time | false     | long     | Far point of time of the query window (unix time in millisecond). Searching based on transact-time. The maximum size of the query window is 48 hour. The query window can be shifted within 120 days. | ((end-time) – 48hour)                                        | [((end-time) – 48hour), (end-time)]                          |
| end-time   | false     | long     | Near point of time of the query window (unix time in millisecond). Searching based on transact-time. The maximum size of the query window is 48 hour. The query window can be shifted within 120 days. | current-time                                                 | [(current-time) – 120days,(current-time)]                    |
| from       | string    | false    | N/A                                                          | Search internal id to begin with                             | if search next page, then this should be the last id (not trade-id) of last page; if search previous page, then this should be the first id (not trade-id) of last page |
| direct     | string    | false    | next                                                         | Search direction when 'from' is used                         | next, prev                                                   |
| size       | int       | false    | 100                                                          | The number of orders to return                               | [1, 500]                                                     |

> The above command returns JSON structured like this:

```json
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
      "role": "taker",
      "filled-points": "0.0",
      "fee-deduct-currency": "",
      "fee-deduct-state": "done"
    }
  ]
```

### Response Content

<aside class="notice">The return data contains a list and each item in the list represents a match result</aside>
| Field               | Data Type | Description                                                  |
| ------------------- | --------- | ------------------------------------------------------------ |
| id                  | long      | Record id, non sequential, it can be used in "from" field for next request |
| symbol              | string    | The trading symbol to trade, e.g. btcusdt, bccbtc            |
| order-id            | long      | The order id of this order                                   |
| match-id            | long      | The match id of this match                                   |
| trade-id            | long      | Unique trade ID                                              |
| price               | string    | The limit price of limit order                               |
| created-at          | long      | The timestamp in milliseconds when this record is created    |
| type                | string    | All possible order type (refer to introduction in this section) |
| filled-amount       | string    | The amount which has been filled                             |
| filled-fees         | string    | Transaction fee (positive value). If maker rebate applicable, revert maker rebate value per trade (negative value). |
| fee-currency        | string    | Currency of transaction fee or transaction fee rebate (transaction fee of buy order is based on base currency, transaction fee of sell order is based on quote currency; transaction fee rebate of buy order is based on quote currency, transaction fee rebate of sell order is based on base currency) |
| source              | string    | All possible order source (refer to introduction in this section) |
| role                | string    | The role in the transaction: taker or maker.                 |
| filled-points       | string    | deduction amount (unit: in ht or hbpoint)                    |
| fee-deduct-currency | string    | deduction type: ht or hbpoint.                               |
| fee-deduct-state | string    | Fee deduction status，In deduction：ongoing，Deduction completed：done |

Notes:<br>

- The calculated maker rebate value inside ‘filled-fees’ would not be paid immediately.<br>


### Error code for invalid start-date/end-date

| err-code           | scenarios                                                    |
| ------------------ | ------------------------------------------------------------ |
| invalid_interval   | Start date is later than end date; the date between start date and end date is greater than 2 days |
| invalid_start_date | Start date is a future date; or start date is earlier than 61 days ago. |
| invalid_end_date   | end date is a future date; or end date is earlier than 61 days ago. |

## Get Current Fee Rate Applied to The User

This endpoint returns the current transaction fee rate applied to the user.

API Key Permission：Read


### HTTP Request

`GET /v2/reference/transact-fee-rate`

### Request Parameters

| Parameter | Data Type | Required | Default | Description                                      | Value Range                       |
| --------- | --------- | -------- | ------- | ------------------------------------------------ | --------------------------------- |
| symbols   | string    | true     | NA      | The trading symbols to query, separated by comma | Refer to `GET /v1/common/symbols` |

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

### Response Content

|      | Field Name        | Data Type | Description                                                  |      |
| ---- | ----------------- | --------- | ------------------------------------------------------------ | ---- |
|      | code              | integer   | Status code                                                  |      |
|      | message           | string    | Error message (if any)                                       |      |
|      | data              | object    |                                                              |      |
|      | { symbol          | string    | Trading symbol                                               |      |
|      | makerFeeRate      | string    | Basic fee rate – passive side (positive value);If maker rebate applicable, revert maker rebate rate (negative value). |      |
|      | takerFeeRate      | string    | Basic fee rate – aggressive side                             |      |
|      | actualMakerRate   | string    | Deducted fee rate – passive side (positive value). If deduction is inapplicable or disabled, return basic fee rate.If maker rebate applicable, revert maker rebate rate (negative value). |      |
|      | actualTakerRate } | string    | Deducted fee rate – aggressive side. If deduction is inapplicable or disabled, return basic fee rate. |      |

Note：
- If makerFeeRate/actualMakerRate is positive，this field means the transaction fee rate.
- If makerFeeRate/actualMakerRate is negative, this field means the rebate fee rate.

## Error Code

Below is the error code and description returned by Trading APIs

| Error Code                                                   | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| base-argument-unsupported                                    | The specified parameter is not supported                     |
| base-system-error                                            | System internel error. For placing or canceling order, it is mostly due to cache issue, please try again later. |
| login-required                                               | Signature is missing, or user not find (key and uid not match).                                        |
| parameter-required                                           | Stop-price or operator parameter is missing for stop-order type |
| base-record-invalid                                          | Failed to get data, please try again later                   |
| order-amount-over-limit                                      | The amount of order exceeds the limitation                   |
| base-symbol-trade-disabled                                   | The symbol is disabled for trading                           |
| base-operation-forbidden                                     | The operation is forbidden for current user or the symbol is not allowed to trade over OTC |
| account-get-accounts-inexistent-error                        | The account doesn't exist in current user                    |
| account-account-id-inexistent                                | The account id doesn't exist                                 |
| sub-user-auth-required                                       | Isolated margin account is not enabled for sub user          |
| order-disabled                                               | The symbol is pending and not allowed to place order         |
| cancel-disabled                                              | The symbol is pending and not allowed to cancel order        |
| order-invalid-price                                          | The order price is invalid, usually exceeds the 10% of latest trade price |
| order-accountbalance-error                                   | The account balance is insufficient                          |
| order-limitorder-price-min-error                             | Sell price cannot be lower than specific price               |
| order-limitorder-price-max-error                             | Buy price cannot be higher than specific price               |
| order-limitorder-amount-min-error                            | Limit order amount can not be less than specific number      |
| order-limitorder-amount-max-error                            | Limit order amount can not be more than specific number      |
| order-etp-nav-price-min-error                                | Order price cannot be lower than specific percentage         |
| order-etp-nav-price-max-error                                | Order price cannot be higher than specific percentage        |
| order-orderprice-precision-error                             | Order price precision error                                  |
| order-orderamount-precision-error                            | Order amount precision error                                 |
| order-value-min-error                                        | Order value cannot be lower than specific value              |
| order-marketorder-amount-min-error                           | Market order sell amount cannot be less than specific amount |
| order-marketorder-amount-buy-max-error                       | Market order buy amount(value) cannot be more than specific amount(value) |
| order-marketorder-amount-sell-max-error                      | Market order sell amount cannot be more than specific amount |
| order-holding-limit-failed                                   | Exceed the holding limit of the currency                     |
| order-type-invalid                                           | Order type is invalid                                        |
| order-orderstate-error                                       | Order state is invalid                                       |
| order-date-limit-error                                       | Order query date exceed the limit                            |
| order-source-invalid                                         | Order source is invalid                                      |
| order-update-error                                           | Order update error                                           |
| order-fl-cancellation-is-disallowed                          | Liquidation order cannot be canceled                         |
| operation-forbidden-for-fl-account-state                     | The operation is forbidden when the account is in liquidation |
| operation-forbidden-for-lock-account-state                   | The operation is forbidden when the account is locked        |
| fl-order-already-existed                                     | An unfilled liquidation order already exists                 |
| order-user-cancel-forbidden                                  | IOC or FOK order is not allowed to cancel                    |
| account-state-invalid                                        | Invalid status of liquidation account                        |
| order-price-greater-than-limit                               | Order price is higher than the limitation before market opens |
| order-price-less-than-limit                                  | Order price is lower than the limitation before market opens |
| order-stop-order-hit-trigger                                 | The stop orders triggered immediately are not allowed        |
| market-orders-not-support-during-limit-price-trading         | Market orders are not supported during limit-price trading   |
| price-exceeds-the-protective-price-during-limit-price-trading | The price exceeds the protective price during limit-price trading |
| invalid-client-order-id                                      | The parameter client order id is duplicated (within last 24h) in place or cancel order request |
| invalid-interval                                             | Query window is zero, negative or greater than limitation    |
| invalid-start-date                                           | The start date is invalid                                    |
| invalid-end-date                                             | The end date is invalid                                      |
| invalid-start-time                                           | The start time is invalid                                    |
| invalid-end-time                                             | The end time is invalid                                      |
| validation-constraints-required                              | The specified parameters is missing                          |
| symbol-not-support                                           | The symbol is not support for cross margin or C2C            |
| not-found                                                    | The order id is not found                                    |
| base-not-found                                               | The record is not found |

## FAQ

### Q1：What is client-order-id?
A： The `client-order-id` is an optional request parameter while placing order. It's string type which maximum length is 64. The client order id is generated by client, and is only valid within 8 hours (It’s only valid within 2 hours for the final state).

### Q2：How to get the order size, price and decimal precision?
A： You can call API `GET /v1/common/symbols` to get the currency pair information, pay attention to the difference between the minimum amount and the minimum price.   

Below are common errors:

- order-value-min-error: The order price is less than minimum price
- order-orderprice-precision-error : The precision for limited order price is wrong 
- order-orderamount-precision-error : The precision for limited order amount is wrong
- order-limitorder-price-max-error : The limited order price is higher than the threshold
- order-limitorder-price-min-error : The limited order price is lower than the threshold
- order-limitorder-amount-max-error : The limited order amount is larger than the threshold
- order-limitorder-amount-min-error : The limited order amount is smaller than the threshold  

### Q3：Why I got insufficient balance error while placing an order just after a successful order matching?
A：To ensure the low latency of order update, Order update push is made directly after order matching. Meanwhile, the clearing service of that order may be still in progress at backend. It is suggested to follow either of below to ensure a successful order submission:

1、Subscribe to WebSocket topic `accounts` for getting account balance moves to ensure the completion of asset clearing.

2、After receiving WebSocket push message, check account balance from REST endpoint to ensure sufficient available balance for the next order submission.

3、Leave sufficient balance in your account.

### Q4: What is the difference between 'filled-fees' and 'filled-points' in match result?
A: Transaction fee can be paid from either of below. They won't exist at the same time.

1、filled-fees: Filled-fee is also called transaction fee. It's charged from your income currency from the transaction. For example, if your purchase order of BTC/USDT got matched，the transaction fee will be based on BTC.

2、filled-points: If user enabled transaction fee deduction, the fee should be charged from either HT or Point. When there's sufficient fund in HT/Point, filled-fees is empty while filled-points has value. That means the deduction is made via HT/Point. User could refer to field `fee-deduct-currency` to get the exact deduction type of the transaction. 

### Q5: What is the difference between 'match-id' and 'trade-id' in matching result?
A: The `match-id` is the identity for order matching, while the `trade-id` is the unique identifier for each trade. One `match-id` may be correlated with multiple `trade-id`, or no `trade-id`(if the order is cancelled). For example, if a taker's order got matched with 3 maker's orders at the same time, it generates 3 trade IDs but only one match ID.

### Q6: Why the order submission could be rejected even though the order price is set as same as current best bid (or best ask)?
A: For some extreme illiquid trading symbols, the best quote price at particular time might be far away from last trade price. But the price limit is actually based on last trade price which could possibly exclude best quote price from valid range for any new order. It is suggested to place orders based on the WebSocket pushed Bid and market data.

### Q7: How to retrieve the trading symbols for margin trade

A: You can get details from Rest API ` GET /v1/common/symbols`. The `leverage-ratio` represents the isolated-margin ratio. The `super-margin-leverage-ratio` represents the cross-margin.

The value `0` indicates that the trading symbols doesn't support margin trading.

# Conditional Order

## Introduction

By comparing with the existing stop limit order, the newly introduced conditional order does have following major differences:<br>

1)	Although the newly introduced conditional order is also triggered by stop price, before it being triggered, the Exchange will not lock order margin for this order. Only when this conditional order being successfully triggered, its order margin will be locked.<br>
2)	Conditional order does support not only limit order type but also market order type. (Trailing stop order only supports market order type.)<br>
3)	As advanced conditional order, trailing stop order does support additional triggering condition i.e. trailing rate. Only when latest market price breaks stop price, and continues to go up (or down), and then reverts back for a certain percentage which exceeding the pre-defined "trailing rate", this order can be triggered. The valid value range of trailing rate is between 0.1% and 5%.<br>

<aside class="notice">All endpoints in this section require authentication</aside>
<aside class="notice">After the official launch of conditional order, Huobi Hong Kong Global might decommission the existing stop order later. This will be notified through another circular.</aside>

## Place a conditional order

POST /v2/algo-orders<br>
API Key Permission: Trade<br>
Rate Limit (NEW): 20times/2sec<br>
Conditional order can be only placed via this endpoint instead of any endpoint in "Trading" section.<br>

### Request Parameter
|	Field	|	Data Type	|	Mandatory	|	Default Value|	Description	|	Valid Value	|
|	-----	|	-----	|	------	|	----	|	------	|	----	|
|	accountId	|	integer	|	TRUE	|		|	Account ID	|At present only support spot account id, margin account id, super-margin account. C2C margin account id is not supported at this point of time.		|
|	symbol	|	string	|	TRUE	|		|	Trading symbol	|		|
|	orderPrice	|	string	|	FALSE	|		|	Order price (invalid for market order) 	|		|
|	orderSide	|	string	|	TRUE	|		|	Order side	|	buy,sell	|
|	orderSize	|	string	|	FALSE	|		|	Order size (invalid for market buy order) 	|		|
|	orderValue	|	string	|	FALSE	|		|	Order value (only valid for market buy order) 	|		|
|	timeInForce	|	string	|	FALSE	|	gtc for orderType=limit; ioc for orderType=market	|	Time in force	|	gtc (invalid for orderType=market), boc (invalid for orderType=market), ioc, fok (invalid for orderType=market)	|
|	orderType	|	string	|	TRUE	|		|	Order type	|	limit,market	|
|	clientOrderId	|	string	|	TRUE	|		|	Client order ID (max length 64-char) 	|		|
|	stopPrice	|	string	|	TRUE	|		|	Stop price	|		|
|	trailingRate	|	string	|	FALSE	|		|	Trailing rate (only valid for trailing stop order)	|	[0.001,0.050]	|

Note:<br>
•	The gap between orderPrice and stopPrice shouldn't exceed the price limit ratio. For example, a limit buy order's price couldn't be higher than 110% of market price, this limitation should be also applicable to orderPrice/stopPrice ratio.<br>
•	User has to make sure the clientOrderId's uniqueness. While the conditional order being triggered, if the clientOrderId is duplicated with another order (within 24hour) coming from same user, the conditional order will fail triggering.<br>
•	User has to make sure the corresponding account has sufficient fund for triggering this conditional order, otherwise it would cause conditional order triggering failure.<br>
•	timeInForce enum values: gtc - good till cancel，boc - book or cancel (also called as post only, or book only), ioc - immediate or cancel, fok - fill or kill<br>

> Response

```json
{
    "code": 200,
    "data": {
        "clientOrderId": "a001"
    }
}
```

### Response Content
|	Field	|	Data Type	|	Mandatory	|	Description	|
|	-----	|	-----	|	------	|	----	|
|	code	|	integer	|	TRUE	|Status code	|
|	message	|	string	|	FALSE	|Error message (if any)	|
|	data	|	object	|	TRUE	|	|
|	{ clientOrderId }	|	string	|	TRUE	|Client order ID	|

## Cancel conditional orders (before triggering)

POST /v2/algo-orders/cancellation<br>
API Key Permission: Trade<br>
Rate Limit (NEW): 20times/2sec<br>
This endpoint only supports order cancellation for those conditional orders which have not triggered yet. To cancel a triggered order, please refer to the endpoints in "Trading" section.<br>
Before a conditional order triggering, it can be only cancelled via this endpoint instead of any endpoint in "Trading" section.<br>

### Request Parameter
|	Field	|	Data Type	|	Mandatory	|	Default Value|	Description	|	Valid Value	|
|	-----	|	-----	|	------	|	----	|	------	|	----	|
|	clientOrderIds	|	string[]	|	TRUE	|		|	Client order ID (maximum 50 orders are allowed, separated by comma) 	|		|

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

### Response Content
|	Field	|	Data Type	|	Mandatory	|	Description	|
|	-----	|	-----	|	------	|	----	|
|	code	|	integer	|	TRUE	|Status code	|
|	message	|	string	|	FALSE	|Error message (if any)	|
|	data	|	object	|	TRUE	|	|
|	{ accepted	|	string[]	|	FALSE	| Accepted clientOrderId list	|
|	rejected }	|	string[]	|	TRUE	| Rejected clientOrderId list	|

## Query open conditional orders (before triggering)

GET /v2/algo-orders/opening<br>
API Key Permission: Read<br>
Rate Limit (NEW): 20times/2sec<br>
Search by orderOrigTime<br>
This endpoint only returns those conditional orders which have not triggered with orderStatus value as created.<br>
Before a conditional order triggering, it can be queried out through this endpoint instead of any endpoint in "Trading" section.<br>

### Request Parameter
|	Field	|	Data Type	|	Mandatory	|	Default Value|	Description	|	Valid Value	|
|	-----	|	-----	|	------	|	----	|	------	|	----	|
|	accountId	|	integer	|	FALSE	|	all	|	Account ID	|		|
|	symbol	|	string	|	FALSE	|	all	|	Trading symbol	|		|
|	orderSide	|	string	|	FALSE	|	all	|	Order side	|	buy,sell	|
|	orderType	|	string	|	FALSE	|	all	|	Order type	|	limit,market	|
|	sort	|	string	|	FALSE	|	desc	|	Sorting order 	|asc, desc	|
|	limit	|	integer	|	FALSE	|	100	|	Maximum number of items in one page	|[1,500]		|
|	fromId	|	long	|	FALSE	|		|	First record ID in this query (only valid for next page querying) 	|		|

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

### Response Content
|	Field	|	Data Type	|	Mandatory	|	Description	|
|	-----	|	-----	|	------	|	----	|
|	code	|	integer	|	TRUE	|Status code	|
|	message	|	string	|	FALSE	|Error message (if any)	|
|	data	|	object	|	TRUE	|In ascening/descending order defined in 'sort'	|
|	{ accountId	|	integer	|	TRUE	|Account ID	|
|	source	|	string	|	TRUE	|Order source (api,web,ios,android,mac,windows,sys) 	|
|	clientOrderId	|	string	|	TRUE	|Client order ID	|
|	symbol	|	string	|	TRUE	|Trading symbol	|
|	orderPrice	|	string	|	TRUE	|Order price (invalid for market order) 	|
|	orderSize	|	string	|	FALSE	|Order size (invalid for market buy order) 	|
|	orderValue	|	string	|	FALSE	|Order value (only valid for market buy order) 	|
|	orderSide	|	string	|	TRUE	|Order side	|
|	timeInForce	|	string	|	TRUE	|Time in force|
|	orderType	|	string	|	TRUE	|Order type	|
|	stopPrice	|	string	|	TRUE	|Stop price	|
|	trailingRate	|	string	|	FALSE	|	Trailing rate (only valid for trailing stop order)	|
|	orderOrigTime	|	long	|	TRUE	|Order original time	|
|	lastActTime	|	long	|	TRUE	|Order last activity time	|
|	orderStatus }	|	string	|	TRUE	|Order status (created) 	|
|	nextId	|	long	|	FALSE	|First record ID in next page (only valid if exceeded page size) 	|

## Query conditional order history

GET /v2/algo-orders/history<br>
API Key Permission: Read<br>
Rate Limit (NEW): 20times/2sec<br>
Search by orderOrigTime<br>
This endpoint only returns those conditional orders which have been cancelled before triggering (orderStatus=canceled), or which have failed to trigger (orderStatus=rejected), or which have successfully triggered (orderStatus=triggered).<br>
To further query the latest status of a successfully triggered conditonal order, please refer to the endpoints in "Trading" section.<br>
The cancelled conditional order before triggering, as well as the conditional order failed to trigger, can be queried out through this endpoint instead of any endpoint in "Trading" section.<br>

### Request Parameter
|	Field	|	Data Type	|	Mandatory	|	Default Value|	Description	|	Valid Value	|
|	-----	|	-----	|	------	|	----	|	------	|	----	|
|	accountId	|	integer	|	FALSE	|	all	|	Account ID	|		|
|	symbol	|	string	|	TRUE	|		|	Trading symbol	|		|
|	orderSide	|	string	|	FALSE	|	all	|	Order side	|	buy,sell	|
|	orderType	|	string	|	FALSE	|	all	|	Order type	|	limit,market	|
|	orderStatus	|	string	|	TRUE	|		|	Order status	|	canceled,rejected,triggered	|
|	startTime	|	long	|	FALSE	|		|	Farthest time	|
|	endTime	|	long	|	FALSE	|current time		|	Nearest time | |
|	sort	|	string	|	FALSE	|	desc	|	Sorting order 	|asc, desc	|
|	limit	|	integer	|	FALSE	|	100	|	Maximum number of items in one page	|[1,500]		|
|	fromId	|	long	|	FALSE	|		|	First record ID in this query (only valid for next page querying) 	|		|

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

### Response Content
|	Field	|	Data Type	|	Mandatory	|	Description	|
|	-----	|	-----	|	------	|	----	|
|	code	|	integer	|	TRUE	|Status code	|
|	message	|	string	|	FALSE	|Error message (if any)	|
|	data	|	object	|	TRUE	|In ascening/descending order defined in 'sort'	|
|	{ accountId	|	integer	|	TRUE	|Account ID	|
|	source	|	string	|	TRUE	|Order source	|
|	clientOrderId	|	string	|	TRUE	|Client order ID	|
|	orderId	|	string	|	FALSE	|Order ID (only valid for orderStatus=triggered)	|
|	symbol	|	string	|	TRUE	|Trading symbol	|
|	orderPrice	|	string	|	TRUE	|Order price (invalid for market order) 	|
|	orderSize	|	string	|	FALSE	|Order size (invalid for market buy order) 	|
|	orderValue	|	string	|	FALSE	|Order value (only valid for market buy order) 	|
|	orderSide	|	string	|	TRUE	|Order side	|
|	timeInForce	|	string	|	TRUE	|Time in force|
|	orderType	|	string	|	TRUE	|Order type	|
|	stopPrice	|	string	|	TRUE	|Stop price	|
|	trailingRate	|	string	|	FALSE	|	Trailing rate (only valid for trailing stop order)	|
|	orderOrigTime	|	long	|	TRUE	|Order original time	|
|	lastActTime	|	long	|	TRUE	|Order last activity time	|
|	orderCreateTime	|	long	|	FALSE	|Order trigger time (only valid for orderStatus=triggered) 	|
|	orderStatus	|	string	|	TRUE	|Order status (triggered,canceled,rejected) 	|
|	errCode	|	integer	|	FALSE	|Status code in case of order triggering failure (only valid for orderStatus=rejected) 	|
|	errMessage }	|	string	|	FALSE	|Error message in case of order triggering failure (only valid for orderStatus=rejected) 	|
|	nextId	|	long	|	FALSE	|First record ID in next page (only valid if exceeded page size) 	|

## Query a specific conditional order

GET /v2/algo-orders/specific<br>
API Key Permission: Read<br>
Rate Limit (NEW): 20times/2sec<br>
Search by orderOrigTime<br>
To further query the latest status of a successfully triggered conditonal order, please refer to the endpoints in "Trading" section.<br>
The conditional order before triggering, as well as the conditional order failed to trigger, can be queried out through this endpoint instead of any endpoint in "Trading" section.<br>

### Request Parameter
|	Field	|	Data Type	|	Mandatory	|	Default Value|	Description	|	Valid Value	|
|	-----	|	-----	|	------	|	----	|	------	|	----	|
|	clientOrderId	|	string	|	TRUE	|		|	Client order ID	|		|

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

### Response Content
|	Field	|	Data Type	|	Mandatory	|	Description	|
|	-----	|	-----	|	------	|	----	|
|	code	|	integer	|	TRUE	|Status code	|
|	message	|	string	|	FALSE	|Error message (if any)	|
|	data	|	object	|	TRUE	|	|
|	{ accountId	|	integer	|	TRUE	|Account ID	|
|	source	|	string	|	TRUE	|Order source	|
|	clientOrderId	|	string	|	TRUE	|Client order ID	|
|	orderId	|	string	|	FALSE	|Order ID (only valid for orderStatus=triggered)	|
|	symbol	|	string	|	TRUE	|Trading symbol	|
|	orderPrice	|	string	|	TRUE	|Order price (invalid for market order) 	|
|	orderSize	|	string	|	FALSE	|Order size (invalid for market buy order) 	|
|	orderValue	|	string	|	FALSE	|Order value (only valid for market buy order) 	|
|	orderSide	|	string	|	TRUE	|Order side	|
|	timeInForce	|	string	|	TRUE	|Time in force|
|	orderType	|	string	|	TRUE	|Order type	|
|	stopPrice	|	string	|	TRUE	|Stop price	|
|	trailingRate	|	string	|	FALSE	|	Trailing rate (only valid for trailing stop order)	|
|	orderOrigTime	|	long	|	TRUE	|Order original time	|
|	lastActTime	|	long	|	TRUE	|Order last activity time	|
|	orderCreateTime	|	long	|	FALSE	|Order trigger time (only valid for orderStatus=triggered) 	|
|	orderStatus	|	string	|	TRUE	|Order status (created,triggered,canceled,rejected) 	|
|	errCode	|	integer	|	FALSE	|Status code in case of order triggering failure (only valid for orderStatus=rejected) 	|
|	errMessage }	|	string	|	FALSE	|Error message in case of order triggering failure (only valid for orderStatus=rejected) 	|

## Error Code

Below is the error code and the description returned by Conditional Order APIs

| Error Code | Description                                          |
| ---------- | ---------------------------------------------------- |
| 1001       | Request URL is invalid                               |
| 1002       | Signature is missing or account id doesn't exist     |
| 1003       | Signature is wrong                                   |
| 1006       | Exceed rate limit                                    |
| 1007       | Record is not found                                  |
| 2002       | Specified parameter is missing or invalid            |
| 2003       | Trading is disabled                                  |
| 3002       | Order amount precision error                         |
| 3003       | Trigger price precision error                        |
| 3004       | Limit order amount is less than minimum amount       |
| 3005       | Limit order amount is greater than maximum amount    |
| 3006       | Limit order price is higher than maximum price       |
| 3007       | Limit order price is lower than minimum price        |
| 3008       | Order value is less than minimum value               |
| 3009       | Market order amount is less than minimum amount      |
| 3010       | Market order amount is greater than maximum amount   |
| 3100       | Market orders can be accepted during limit price trading |

# Websocket Market Data

## Introduction

### Websocket URL

**Websocket Market Feed (excluding MBP incremental channel & its REQ channel)**

**`wss://api.huobi.com.hk/ws`**


**MBP incremental channel & its REQ channel)**

**`wss://api.huobi.com.hk/feed`**


### Data Format

All return data of websocket Market APIs are compressed with GZIP so they need to be unzipped.

### Heartbeat and Connection

```json
{"ping": 1492420473027} 
```
After connected to Huobi Hong Kong's Websocket server, the server will send heartbeat periodically (currently at 5s interval). The heartbeat message will have an integer in it, e.g.

```json
{"pong": 1492420473027} 
```
When client receives this heartbeat message, it should respond with a matching "pong" message which has the same integer in it, e.g.

<aside class="warning">After the server sent two consecutive heartbeat messages without receiving at least one matching "pong" response from a client, then right before server sends the next "ping" heartbeat, the server will be disconnected with the client server.</aside>

### Subscribe to Topic

> Sub request:

```json
{
  "sub": "market.btcusdt.kline.1min",
  "id": "id1"
}
```

To receive data you have to send a "sub" message first.

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

After successfully subscribed, you will receive a response to confirm subscription

Then, you will received messages when there is any update in the subcribed topics.

### Unsubscribe

> UnSub request:

```json
{
  "unsub": "market.btcusdt.trade.detail",
  "id": "id4"
}
```

To unsubscribe, you need to send below message

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

And you will receive a message to confirm the action.

### Pull Data

While connected to websocket, you can also use it in pull style by sending message to the server.

To request pull style data, you send below message

{
  "req": "topic to req",
  "id": "id generate by client"
}

You will receive a response accordingly and immediately

### Rate Limit

**Rate limt of pull style query (req)**

The limitation of single connection is 100 ms.

## Market Candlestick

This topic sends a new candlestick whenever it is available.

### Topic

`market.$symbol$.kline.$period$`

> Subscribe request

```json
{
  "sub": "market.ethbtc.kline.1min",
  "id": "id1"
}
```

### Topic Parameter

| Parameter | Data Type | Required | Description          | Value Range                                                  |
| --------- | --------- | -------- | -------------------- | ------------------------------------------------------------ |
| symbol    | string    | true     | Trading symbol       | All supported trading symbol, e.g. btcusdt, bccbtc. (to retrieve candlesticks for ETP NAV, symbol = ETP trading symbol + suffix 'nav'，for example: btc3lusdtnav) |
| period    | string    | true     | Candlestick interval | 1min, 5min, 15min, 30min, 60min, 4hour, 1day, 1mon, 1week, 1year |

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

### Update Content

| Field  | Data Type | Description                                                  |
| ------ | --------- | ------------------------------------------------------------ |
| id     | integer   | UNIX epoch timestamp in second as response id                |
| amount | float     | Aggregated trading volume during the interval (in base currency) |
| count  | integer   | Number of trades during the interval                         |
| open   | float     | Opening price during the interval                            |
| close  | float     | Closing price during the interval                            |
| low    | float     | Low price during the interval                                |
| high   | float     | High price during the interval                               |
| vol    | float     | Aggregated trading value during the interval (in quote currency) |

<aside class="notice">When symbol is set to "hb10" or "huobi10", amount, count, and vol will always have the value of 0</aside>
### Pull Request

Pull request is supported with extra parameters to define the range. The maximum number of ticks in each response is 300.

```json
{
  "req": "market.$symbol.kline.$period",
  "id": "id generated by client",
  "from": "from time in epoch seconds",
  "to": "to time in epoch seconds"
}
```

| Parameter | Data Type | Required | Default Value                         | Description                        | Value Range                                                  |
| --------- | --------- | -------- | ------------------------------------- | ---------------------------------- | ------------------------------------------------------------ |
| from      | integer   | false    | 1501174800(2017-07-28T00:00:00+08:00) | "From" time (epoch time in second) | [1501174800, 2556115200]                                     |
| to        | integer   | false    | 2556115200(2050-01-01T00:00:00+08:00) | "To" time (epoch time in second)   | [1501174800, 2556115200] or ($from, 2556115200] if "from" is set |

## Market Ticker

Retrieve the market ticker,data is pushed every 100ms.

### Topic

`market.$symbol.ticker`

> Subscribe request

```json
{
  "sub": "market.ethbtc.ticker",
  "id": "id1"
}
```

### Request Parameters

| Parameter | Data Type | Required | Default | Description                 | Value Range                                                  |
| --------- | --------- | -------- | ------- | --------------------------- | ------------------------------------------------------------ |
| symbol    | string    | true     | NA      | The trading symbol to query | All supported trading symbol, e.g. btcusdt, bccbtc.Refer to `/v1/common/symbols` |

> The above command returns JSON structured like this:

```json
"data": {
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

### Response Content

| Field  | Data Type | Description                                                  |
| ------ | --------- | ------------------------------------------------------------ |
| id     | long      | The internal identity                                        |
| amount | float     | Accumulated trading volume of last 24 hours (rotating 24h), in base currency |
| count  | integer   | The number of completed trades (rotating 24h)                |
| open   | float     | The opening price of last 24 hours (rotating 24h)            |
| close  | float     | The last price of last 24 hours (rotating 24h)               |
| low    | float     | The lowest price of last 24 hours (rotating 24h)             |
| high   | float     | The highest price of last 24 hours (rotating 24h)            |
| vol    | float     | Accumulated trading value of last 24 hours (rotating 24h), in quote currency |
| bid    | object    | The current best bid in format [price, size]                 |
| ask    | object    | The current best ask in format [price, size]                 |

## 

## Market Depth

This topic sends the latest market by price order book in snapshot mode at 1-second interval.

### Topic

`market.$symbol.depth.$type`

> Subscribe request

```json
{
  "sub": "market.btcusdt.depth.step0",
  "id": "id1"
}
```

### Topic Parameter

| Parameter | Data Type | Required | Default Value | Description                                   | Value Range                              |
| --------- | --------- | -------- | ------------- | --------------------------------------------- | ---------------------------------------- |
| symbol    | string    | true     | NA            | Trading symbol                                | Refer to `GET /v1/common/symbols`        |
| type      | string    | true     | step0         | Market depth aggregation level, details below | step0, step1, step2, step3, step4, step5 |

**"type" Details**

| Value | Description                          |
| ----- | ------------------------------------ |
| step0 | No market depth aggregation          |
| step1 | Aggregation level = precision*10     |
| step2 | Aggregation level = precision*100    |
| step3 | Aggregation level = precision*1000   |
| step4 | Aggregation level = precision*10000  |
| step5 | Aggregation level = precision*100000 |

While type is set as ‘step0’, the market depth data supports up to 150 levels.
While type is set as ‘step1’, ‘step2’, ‘step3’, ‘step4’, or ‘step5’, the market depth data supports up to 20 levels.

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

### Update Content

<aside class="notice">Under 'tick' object there is a list of bids and a list of asks</aside>
| Field   | Data Type | Description                                                  |
| ------- | --------- | ------------------------------------------------------------ |
| bids    | object    | The current all bids in format [price, size]                 |
| asks    | object    | The current all asks in format [price, size]                 |
| version | integer   | Internal data                                                |
| ts      | integer   | The UNIX timestamp in milliseconds adjusted to Singapore time |

<aside class="notice">When symbol is set to "hb10" amount, count, and vol will always have the value of 0</aside>
### Pull Request

Pull request is supported.

```json
{
  "req": "market.btcusdt.depth.step0",
  "id": "id10"
}
```

## Market By Price (incremental update)

User could subscribe to this channel to receive incremental update of Market By Price order book. Refresh message, the full image of the order book, are acquirable from the same channel, upon "req" request.

**MBP incremental channel & its REQ channel)**

**`wss://api.huobi.com.hk/feed`**


Suggested downstream data processing:<br>
1)	Subscribe to incremental updates and start to cache them;<br>
2)	Request refresh message (with same number of levels), and base on its “seqNum” to align it with the cached incremental message which has the same “prevSeqNum”;<br>
3)	Start to continuously process incremental messages to build up MBP book;<br>
4)	The “prevSeqNum” of the current incremental message must be the same with “seqNum” of the previous message, otherwise it implicates message loss which should require another round of refresh message retrieval and alignment;<br>
5)	Once received a new price level from incremental message, that price level should be inserted into appropriate position of existing MBP book;<br>
6)	Once received an updated “size” at the existing price level from incremental message, the size should be replaced directly by the new value;<br>
7)	Once received a “size=0” at existing price level from incremental message, that price level should be removed from MBP book;<br>
8)	If one incremental message includes updates of multiple price levels, all of those levels should be updated simultaneously in MBP book.<br>

Currently Huobi Hong Kong Global only supports 5-level/20-level MBP incremental channel and 150-level incremental channel, the differences between them are -<br>
1) Different depth of market.<br>
2) 5-level/20-level incremental MBP is a tick by tick feed, which means whenever there is an order book change at that level, it pushes an update; 150 levels incremental MBP feed is based on the gap between two snapshots at 100ms interval.<br>
3) While there is single side order book update, either bid or ask, the incremental message sent from 5-level/20-level MBP feed only contains that side update. <br>

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
But the incremental message from 150 levels MBP feed contains not only that side update and also a blank object for another side.

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
In the near future, Huobi Hong Kong Global will align the update behavior of 150-level incremental channel with 5-level/20-level, which means while single side order book changed (either bid or ask), the update message will be no longer including a blank object for another side.<br>
4) While there is nothing change between two snapshots in past 100ms, the 150 levels incremental MBP feed still sends out a message which contains two blank objects – bids & asks. <br>

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
But 5-level/20-level incremental channel won’t disseminate any update in such a case.<br>
In the future, Huobi Hong Kong Global will align the update behavior of 150-level incremental channel with 5-level/20-level, which means while there is no order book change at all, the channel will be no longer disseminating messages of blank object any more.<br>
5) 5-level/20-level incremental channel only supports the following symbols at this stage - btcusdt,ethusdt,xrpusdt,eosusdt,ltcusdt,etcusdt,adausdt,dashusdt,bsvusdt, while 150-level incremental channel supports all symbols.<br>

REQ channel supports refreshing message for 5-level, 20-level, and 150-level.

### Subscribe incremntal updates

`market.$symbol.mbp.$levels`

> Sub request

```json
{
  "sub": "market.btcusdt.mbp.5",
  "id": "id1"
}
```

### Request refresh update

`market.$symbol.mbp.$levels`

> Req request

```json
{
  "req": "market.btcusdt.mbp.5",
  "id": "id2"
}
```

### Parameters

| Field Name | Data Type | Mandatory | Default Value | Description                                    | Value Range                                                  |
| ---------- | --------- | --------- | ------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| symbol     | string    | true      | NA            | Trading symbol (wildcard inacceptable)         |                                                              |
| levels     | integer   | true      | NA            | Number of price levels (Valid value: 5,20,150,400) | Only support the number of price levels at 5, 20,150 or 400 at this point of time. |

> Response (Incremental update subscription)

```json
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.mbp.5",
  "ts": 1489474081631 //system response time
}
```

> Incremental Update (Incremental update subscription)

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

> Response (Refresh update acquisition)

```json
{
	"id": "id2",
	"rep": "market.btcusdt.mbp.5",
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

### Update Content

| Field Name | Data Type | Description                                                  |
| ---------- | --------- | ------------------------------------------------------------ |
| seqNum     | integer   | Sequence number of the message                               |
| prevSeqNum | integer   | Sequence number of previous message                          |
| bids       | object    | Bid side, (in descending order of “price”), ["price","size"] |
| asks       | object    | Ask side, (in ascending order of “price”), ["price","size"]  |

## Market By Price (refresh update)

User could subscribe to this channel to receive refresh update of Market By Price order book. The update interval is around 100ms.

### Subscription

`market.$symbol.mbp.refresh.$levels`

```json
{
"sub": "market.btcusdt.mbp.refresh.20",
"id": "id1"
}

```

### Parameters

| Field Name | Data Type | Mandatory | Default Value | Description                            | Value Range |
| ---------- | --------- | --------- | ------------- | -------------------------------------- | ----------- |
| symbol     | string    | true      | NA            | Trading symbol (wildcard inacceptable) |             |
| levels     | integer   | true      | NA            | Number of price levels                 | 5,10,20     |

> Response

```json
{
"id": "id1",
"status": "ok",
"subbed": "market.btcusdt.mbp.refresh.20",
"ts": 1489474081631 //system response time
}
```

> Response

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
			[210.34, 94.463], ... // rest levels omitted
   		],
		"asks": [
			[650.59, 14.909733438479636],
			[650.63, 97.996],
			[650.77, 97.465],
			[651.23, 83.973],
			[651.42, 34.465], ... // rest levels omitted
		]
}
}
```

### Update Content

| Field Name | Data Type | Description                                                  |
| ---------- | --------- | ------------------------------------------------------------ |
| seqNum     | integer   | Sequence number of the message                               |
| bids       | object    | Bid side, (in descending order of “price”), ["price","size"] |
| asks       | object    | Ask side, (in ascending order of “price”), ["price","size"]  |

## Best Bid/Offer

User can receive BBO (Best Bid/Offer) update in tick by tick mode.

### Topic

`market.$symbol.bbo`

> Subscribe request

```json
{
  "sub": "market.btcusdt.bbo",
  "id": "id1"
}
```

### Topic parameter

| Parameter | Data Type | Required | Default Value | Description    | Value Range                       |
| --------- | --------- | -------- | ------------- | -------------- | --------------------------------- |
| symbol    | string    | true     | NA            | Trading symbol | Refer to `GET /v1/common/symbols` |

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
    "askSize": "0.3"
    "seqId": "1276823698734"
  }
}
```

### Update Content

| Field     | Data Type | Description     |
| --------- | --------- | --------------- |
| symbol    | string    | Trading symbol  |
| quoteTime | long      | Quote time      |
| bid       | float     | Best bid        |
| bidSize   | float     | Best bid size   |
| ask       | float     | Best ask        |
| askSize   | float     | Best ask size   |
| seqId     | int       | Sequence number |


## Trade Detail

This topic sends the latest completed trades. It updates in tick by tick mode.

### Topic

`market.$symbol.trade.detail`

> Subscribe request

```json
{
  "sub": "market.btcusdt.trade.detail",
  "id": "id1"
}
```

### Topic Parameter

| Parameter | Data Type | Required | Default Value | Description    | Value Range                       |
| --------- | --------- | -------- | ------------- | -------------- | --------------------------------- |
| symbol    | string    | true     | NA            | Trading symbol | Refer to `GET /v1/common/symbols` |

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
                "tradeId": 102043495674,
                "price": 401.74,
                "direction": "buy"
            }
            // more Trade Detail data here
        ]
  }
}
```

### Update Content

| Field     | Data Type | Description                                                  |
| --------- | --------- | ------------------------------------------------------------ |
| id        | integer   | Unique trade id (to be obsoleted)                            |
| tradeId   | integer   | Unique trade id (NEW)                                        |
| amount    | float     | The volume of the trade (buy side or sell side)                    |
| price     | float     | The price of the trade                                            |
| ts        | integer   | timestamp (UNIX epoch time in millisecond)             |
| direction | string    | direction of the trade (taker): 'buy' or 'sell' |

### Pull Request

Pull request (of maximum latest 300 trade records) is supported.

```json
{
  "req": "market.btcusdt.trade.detail",
  "id": "id11"
}
```

## Market Details

This topic sends the latest market stats with 24h summary. It updates in snapshot mode, in frequency of no more than 10 times per second.

### Topic

`market.$symbol.detail`

> Subscribe request

```json
{
  "sub": "market.btcusdt.detail",
  "id": "id1"
}
```

### Topic Parameter

| Parameter | Data Type | Required | Default Value | Description    | Value Range                       |
| --------- | --------- | -------- | ------------- | -------------- | --------------------------------- |
| symbol    | string    | true     | NA            | Trading symbol | Refer to `GET /v1/common/symbols` |

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
  "ts": 1494497082831, //system update time
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

### Update Content

| Field  | Data Type | Description                                              |
| ------ | --------- | -------------------------------------------------------- |
| id     | integer   | UNIX epoch timestamp in second as response id            |
| amount | float     | Aggregated trading volume in past 24H (in base currency) |
| count  | integer   | Number of trades in past 24H                             |
| open   | float     | Opening price in past 24H                                |
| close  | float     | Last price                                               |
| low    | float     | Low price in past 24H                                    |
| high   | float     | High price in past 24H                                   |
| vol    | float     | Aggregated trading value in past 24H (in quote currency) |



## Error Code

Below is the error code, error message and description returned by Market WebSocket

| Error Code  | Error Message               | Description                            |
| ----------- | --------------------------- | -------------------------------------- |
| bad-request | invalid topic               | Parameter topic is invalid             |
| bad-request | invalid symbol              | Parammeter symbol is invalid           |
| bad-request | symbol trade not open now   | The market is not open for this symbol |
| bad-request | 429 too many request        | Request exceed limit                   |
| bad-request | unsub with not subbed topic | The topic is not subscribed            |
| bad-request | not json string             | The request is not JSON format         |
| bad-request | request timeout             | The request is time out                |

# Websocket Account and Order

## Introduction

### Access URL

**Websocket Asset and Order**

**`wss://api.huobi.com.hk/ws/v2`**  


### Message Compression

Unlike Market WebSocket, the return data of Account and Order Websocket are not compressed by GZIP.

### Heartbeat

```json
{
	"action": "ping",
	"data": {
		"ts": 1575537778295
	}
}
```

Once the Websocket connection is established, Huobi Hong Kong server will periodically send "ping" message at 20s interval, with an integer inside.

```json
{
    "action": "pong",
    "data": {
          "ts": 1575537778295 // the same with "ping" message
    }
}
```

Once client receives "ping", it should respond "pong" message back with the same integer.

### Valid Values of `action`

| Valid Values | Description                                 |
| ------------ | ------------------------------------------- |
| sub          | Subscribe                                   |
| req          | Request                                     |
| ping,pong    | Heartbeat                                   |
| push         | Push (from Huobi Hong Kong server to client's server) |

### Rate Limit

There are multiple limitations for this version:

- The limitation of single connection for **valid** request (including req, sub, unsub, excluding ping/pong or other invalid request) is **50 per second**. It will return "too many request" when the limit is exceeded.
- A single API Key can establish **10** connections. It will return "too many connection" when the limit is exceeded.
- The limitation of requests from single IP is **100 per second**. It will return "too many request" when the limitation is exceeded.

### Authentication

Authentication request field list

| Field            | Mandatory | Data Type | Description                                                  |
| ---------------- | --------- | --------- | ------------------------------------------------------------ |
| action           | true      | string    | Action type, valid value: "req"                              |
| ch               | true      | string    | Channel, valid value: "auth"                                 |
| authType         | true      | string    | Authentication type, valid value: "api". Note: this is not part of signature calculation |
| accessKey        | true      | string    | Access key                                                   |
| signatureMethod  | true      | string    | Signature method, valid value: "HmacSHA256"                  |
| signatureVersion | true      | string    | Signature version, valid value: "2.1"                        |
| timestamp        | true      | string    | Timestamp in UTC in format like 2017-05-11T16:22:06          |
| signature        | true      | string    | Signature                                                    |

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

This is an exmaple of authentication request:

```json
{
	"action": "req",
	"code": 200,
	"ch": "auth",
	"data": {}
}
```

The response of success:

### Generating Signature 

The signature generation method of Account and Order WebSocket is similar with Rest API , with only following differences:

1. The request method should be "GET", to URL "/ws/v2".
2. The involved field names in signature generation are: accessKey，signatureMethod，signatureVersion，timestamp
3. The valid value of signatureVersion is 2.1.

```
GET\n
api.huobi.com.hk\n
/ws/v2\n
accessKey=0664b695-rfhfg2mkl3-abbf6c5d-49810&signatureMethod=HmacSHA256&signatureVersion=2.1&timestamp=2019-12-05T11%3A53%3A03
```

The final string involved in signature generation should be like below:

Note: The data in JSON request doesn't require URL encode

### Subscribe a Topic to Continuously Receive Updates

> Sub request:

```json
{
	"action": "sub",
	"ch": "accounts.update"
}
```

Once the Websocket connection is established, Websocket client could send following request to subscribe a topic:

> Sub respose:

```json
{
	"action": "sub",
	"code": 200,
	"ch": "accounts.update#0",
	"data": {}
}
```

Upon success, Websocket client should receive a response below:

## Subscribe Order Updates

API Key Permission: Read

An order update can be triggered by any of following:<br>
-	Conditional order triggering failure (eventType=trigger)<br>
-	Conditional order cancellation before trigger (eventType=deletion)<br>
-	Order creation (eventType=creation)<br>
-	Order matching (eventType=trade)<br>
-	Order cancellation (eventType=cancellation)<br>

The field list in order update message can be various per event type, developers can design the data structure in either of two ways:<br>
- Define a data structure including fields for all event types, allowing a few of them are null<br>
- Define different data structure for each event type to include specific fields, inheriting from a common data structure which has common fields

### Topic

` orders#${symbol}`

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

### Subscription Field

| Field  | Data Type | Description                            |
| ------ | --------- | -------------------------------------- |
| symbol | string    | Trading symbol (wildcard * is allowed) |

### Update Content

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

After conditional order triggering failure –

| Field         | Data Type | Description                                                  |
| ------------- | --------- | ------------------------------------------------------------ |
| eventType     | string    | Event type, valid value: trigger (only applicable for conditional order) |
| symbol        | string    | Trading symbol                                               |
| clientOrderId | string    | Client order ID                                              |
| orderSide     | string    | Order side, valid value: buy, sell                           |
| orderStatus   | string    | Order status, valid value: rejected                          |
| errCode       | int       | Error code for triggering failure                            |
| errMessage    | string    | Error message for triggering failure                         |
| lastActTime   | long      | Order triggering failure time                                           |

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

After conditional order being cancelled before triggering –

| Field         | Data Type | Description                                                  |
| ------------- | --------- | ------------------------------------------------------------ |
| eventType     | string    | Event type, valid value: deletion (only applicable for conditional order) |
| symbol        | string    | Trading symbol                                               |
| clientOrderId | string    | Client order ID                                              |
| orderSide     | string    | Order side, valid value: buy, sell                           |
| orderStatus   | string    | Order status, valid value: canceled                          |
| lastActTime   | long      | Order trigger time                                           |

```json
{
	"action":"push",
	"ch":"orders#btcusdt",
	"data":
	{
		"orderSize":"2.000000000000000000",
		"orderCreateTime":1583853365586,
		"accountld":992701,
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

After order is submitted –

| Field           | Data Type | Description                                                  |
| --------------- | --------- | ------------------------------------------------------------ |
| eventType       | string    | Event type, valid value: creation                            |
| symbol          | string    | Trading symbol                                               |
| accountId       | long      | account ID                                                   |
| orderId         | long      | Order ID                                                     |
| clientOrderId   | string    | Client order ID (if any)                                     |
| orderSource     | string    | Order source                                                 |
| orderPrice      | string    | Order price                                                  |
| orderSize       | string    | Order size (inapplicable for market buy order)               |
| orderValue      | string    | Order value (only applicable for market buy order)           |
| type            | string    | Order type, valid value: buy-market, sell-market, buy-limit, sell-limit, buy-limit-maker, sell-limit-maker, buy-ioc, sell-ioc, buy-limit-fok, sell-limit-fok |
| orderStatus     | string    | Order status, valid value: submitted                         |
| orderCreateTime | long      | Order creation time                                          |

Note:<br>
- If a stop limit order is created but not yet triggered, the topic won’t send an update.<br>
- The topic will send creation update for taker's order before it being filled.<br>
- Stop limit order's type is no longer as “buy-stop-limit” or “sell-stop-limit”, but changing to “buy-limit” or “sell-limit”.<br>

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

After order matching –

| Field         | Data Type | Description                                                  |
| ------------- | --------- | ------------------------------------------------------------ |
| eventType     | string    | Event type, valid value: trade                               |
| symbol        | string    | Trading symbol                                               |
| tradePrice    | string    | Trade price                                                  |
| tradeVolume   | string    | Trade volume                                                 |
| orderId       | long      | Order ID                                                     |
| type          | string    | Order type, valid value: buy-market, sell-market, buy-limit, sell-limit, buy-limit-maker, sell-limit-maker, buy-ioc, sell-ioc, buy-limit-fok, sell-limit-fok |
| clientOrderId | string    | Client order ID (if any)                                     |
| orderSource   | string    | Order source                                                 |
| orderPrice    | string    | Original order price (not available for market order)        |
| orderSize     | string    | Original order amount (not available for buy-market order)   |
| orderValue    | string    | Original order value (only available for buy-market order)   |
| tradeId       | long      | Trade ID                                                     |
| tradeTime     | long      | Trade time                                                   |
| aggressor     | bool      | Aggressor or not, valid value: true (taker), false (maker)   |
| orderStatus   | string    | Order status, valid value: partial-filled, filled            |
| remainAmt     | string    | Remaining amount (for buy-market order it's remaining value) |
| execAmt       | string    | Accumulative amount (for buy-market order it is accumulative value) |

Note:<br>
- Stop limit order's type is no longer as “buy-stop-limit” or “sell-stop-limit”, but changing to “buy-limit” or “sell-limit”.<br>
- If a taker’s order matching with multiple orders at opposite side simultaneously, the multiple trades will be disseminated over separately instead of merging into one trade.<br>

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

After order cancellation –

| Field         | Data Type | Description                                                  |
| ------------- | --------- | ------------------------------------------------------------ |
| eventType     | string    | Event type, valid value: cancellation                        |
| symbol        | string    | Trading symbol                                               |
| orderId       | long      | Order ID                                                     |
| type          | string    | Order type, valid value: buy-market, sell-market, buy-limit, sell-limit, buy-limit-maker, sell-limit-maker, buy-ioc, sell-ioc, buy-limit-fok, sell-limit-fok |
| clientOrderId | string    | Client order ID (if any)                                     |
| orderSource   | string    | Order source                                                 |
| orderPrice    | string    | Original order price (not available for market order)        |
| orderSize     | string    | Original order amount (not available for buy-market order)   |
| orderValue    | string    | Original order value (only available for buy-market order)   |
| orderStatus   | string    | Order status, valid value: partial-canceled, canceled        |
| remainAmt     | string    | Remaining amount	(for buy-market order it's remaining value) |
| execAmt       | string    | Accumulative amount (for buy-market order it is accumulative value) |
| lastActTime   | long      | Last activity time                                           |

Note:<br>
- Stop limit order's type is no longer as “buy-stop-limit” or “sell-stop-limit”, but changing to “buy-limit” or “sell-limit”.<br>

## Subscribe Trade Details & Order Cancellation post Clearing

API Key Permission: Read

Only update when order is in transaction or cancellation. Order transaction update is in tick by tick mode, which means, if a taker’s order matches with multiple maker’s orders, the simultaneous multiple trades will be disseminated one by one. But the update sequence of the multiple trades, may not be exactly the same as the sequence of the transactions made. Also, if an order is auto cancelled immediately just after its partial fills, for example a typical IOC order, this channel would possibly disseminate the cancellation update first prior to the trade. <br>

If user willing to receive order updates in exact same sequence with the original happening, it is recommended to subscribe order update channel orders#${symbol}.<br>

### Topic

`trade.clearing#${symbol}#${mode}`

### Subscription Field

| Field  | Data Type | Description                                                  |
| ------ | --------- | ------------------------------------------------------------ |
| symbol | string    | Trading symbol (wildcard * is allowed)                       |
| mode   | int       | Subscription mode (0 – subscribe only trade event; 1 – subscribe both trade and cancellation events; default value: 0) |

Note:<br>
About optional field ‘mode’: If not filled, or filled with 0, it implicates to subscribe trade event only. If filled with 1, it implicates to subscribe both trade and cancellation events.<br>


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

### Update Contents (after order matching)

| Field           | Data Type | Description                                                  |
| --------------- | --------- | ------------------------------------------------------------ |
| eventType       | string    | Event type (trade)                                           |
| symbol          | string    | Trading symbol                                               |
| orderId         | long      | Order ID                                                     |
| tradePrice      | string    | Trade price                                                  |
| tradeVolume     | string    | Trade volume                                                 |
| orderSide       | string    | Order side, valid value: buy, sell                           |
| orderType       | string    | Order type, valid value: buy-market, sell-market,buy-limit,sell-limit,buy-ioc,sell-ioc,buy-limit-maker,sell-limit-maker,buy-stop-limit,sell-stop-limit,buy-limit-fok, sell-limit-fok, buy-stop-limit-fok, sell-stop-limit-fok |
| aggressor       | bool      | Aggressor or not, valid value: true, false                   |
| tradeId         | long      | Trade ID                                                     |
| tradeTime       | long      | Trade time, unix time in millisecond                         |
| transactFee     | string    | Transaction fee (positive value) or Transaction rebate (negative value) |
| feeCurrency     | string    | Currency of transaction fee or transaction fee rebate (transaction fee of buy order is based on base currency, transaction fee of sell order is based on quote currency; transaction fee rebate of buy order is based on quote currency, transaction fee rebate of sell order is based on base currency) |
| feeDeduct       | string    | Transaction fee deduction                                    |
| feeDeductType   | string    | Transaction fee deduction type, valid value: ht, point       |
| accountId       | long      | Account ID                                                   |
| source          | string    | Order source                                                 |
| orderPrice      | string    | Order price (invalid for market order)                       |
| orderSize       | string    | Order size (invalid for market buy order)                    |
| orderValue      | string    | Order value (only valid for market buy order)                |
| clientOrderId   | string    | Client order ID                                              |
| stopPrice       | string    | Stop price (only valid for stop limit order)                 |
| operator        | string    | Operation character (only valid for stop limit order)        |
| orderCreateTime | long      | Order creation time                                          |
| orderStatus     | string    | Order status, valid value: filled, partial-filled            |

Notes:<br>
- The calculated maker rebate value inside ‘transactFee’ may not be paid immediately.<br>

### Update Contents (after order cancellation)

| Field           | Data Type | Description                                                  |
| --------------- | --------- | ------------------------------------------------------------ |
| eventType       | string    | Event type (cancellation)                                    |
| symbol          | string    | Trading symbol                                               |
| orderId         | long      | Order ID                                                     |
| orderSide       | string    | Order side, valid value: buy, sell                           |
| orderType       | string    | Order type, valid value: buy-market, sell-market,buy-limit,sell-limit,buy-ioc,sell-ioc,buy-limit-maker,sell-limit-maker,buy-stop-limit,sell-stop-limit,buy-limit-fok, sell-limit-fok, buy-stop-limit-fok, sell-stop-limit-fok |
| accountId       | long      | Account ID                                                   |
| source          | string    | Order source                                                 |
| orderPrice      | string    | Order price (invalid for market order)                       |
| orderSize       | string    | Order size (invalid for market buy order)                    |
| orderValue      | string    | Order value (only valid for market buy order)                |
| clientOrderId   | string    | Client order ID                                              |
| stopPrice       | string    | Stop price (only valid for stop limit order)                 |
| operator        | string    | Operation character (only valid for stop limit order)        |
| orderCreateTime | long      | Order creation time                                          |
| remainAmt       | string    | Remaining order amount (if market buy order, it implicates remaining order value) |
| orderStatus     | string    | Order status, valid value: canceled, partial-canceled        |

## Subscribe Account Change

API Key Permission: Read

The topic updates account change details.

### Topic

`accounts.update#${mode}`

Upon subscription field value specified, the update can be triggered by either of following events:

1、Whenever account balance is changed.

2、Whenever account balance or available balance is changed. (Update separately.)

3、Whenever  account balance or available balance changed, it will be updated together.

Note that right now there is no account update when transferring between spot account and other accounts.

### Subscription Field

| Field | Data Type | Description                                       |
| ----- | --------- | ------------------------------------------------- |
| mode  | integer   | Trigger mode, valid value: 0, 1, default value: 0 |

Samples 

1、Not specifying "mode":  
accounts.update  
Only update when account balance changed;  

2、Specify "mode" as 0:  
accounts.update#0  
Only update when account balance changed;  

3、Specify "mode" as 1:  
accounts.update#1  
Update when either account balance changed or available balance changed.  

4、Specify "mode" as 2:  
accounts.update#2  
Whenever account balance or available balance changed, it will be updated together.

Note:
The topic disseminates the current static value of individual accounts first, at the beginning of subscription, followed by account change updates. While disseminating the current static value of individual accounts, inside the message, field value of "changeType" and "changeTime" is null.

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

### Update Contents

| Field       | Data Type | Description                                                  |
| ----------- | --------- | ------------------------------------------------------------ |
| currency    | string    | Currency                                                     |
| accountId   | long      | Account ID                                                   |
| balance     | string    | Account balance (only exists when account balance changed)   |
| available   | string    | Available balance (only exists when available balance changed) |
| changeType  | string    | Change type, valid value: order-place,order-match,order-refund,order-cancel,order-fee-refund,margin-transfer,margin-loan,margin-interest,margin-repay,deposit,withdraw,other |
| accountType | string    | account type, valid value: trade, loan, interest             |
| changeTime  | long      | Change time, unix time in millisecond                        |

Note:<br>

- A maker rebate would be paid in batch mode for multiple trades.<br>


## Error Code

Below is the return code, return message and the description returend from Asset and Order WebSocket

| Return Code | Return Message           | Description                                                  |
| ----------- | ------------------------ | ------------------------------------------------------------ |
| 200         | Success                  | Successful                                                   |
| 100         | time out close           | The connection is timeout and closed                         |
| 400         | Bad Request              | The request is invalid                                       |
| 404         | Not Found                | The service is not found                                     |
| 429         | Too Many Requests        | Connection number exceed limit                               |
| 500         | system error             | System internal error                                        |
| 2000        | invalid.ip               | The IP is invalid                                            |
| 2001        | nvalid.json              | The JSON request is invalid                                  |
| 2001        | invalid.action           | Parameter action is invalid                                  |
| 2001        | invalid.symbol           | Parameter symbol is invalid                                  |
| 2001        | invalid.ch               | Parameter channel is invalid                                 |
| 2001        | missing.param.auth       | Parameter auth is missing                                    |
| 2002        | invalid.auth.state       | Authentication state is invalid                              |
| 2002        | auth.fail                | Authentication failed, including wrong IP address binding in API Key |
| 2003        | query.account.list.error | Account query error                                          |
| 4000        | too.many.request         | Request exceed limit (a single instance limit to 50 per second) |
| 4000        | too.many.connection      | Connection number exceed limit for single API Key (a single instance limit to 10 connections) |
