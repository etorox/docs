<br>

<br>

# eToroX API Documentation 3rd revision
<br>

<br>

<br>

## General API Information
<hr>

### Rate Limit

Each client may send up to 2000 authorized (private) API requests per second.<br> In case this limit is reached, the consequent requests will receive an HTTP 429 error response.  

<br>
   
### HTTP Return Codes

| HTTP Status | Message Code | Description |
| ----------- | ------------ | ----------- |
| 200 | SUCCESS | |
| 400 | BAD_REQUEST | Usually occurs due to validation errors. |
| 401 | UNAUTHORIZED | Failed authorization process. |
| 403 | FORBIDDEN | Not enough permissions to perform the request. |
| 404 | NOT_FOUND | Wrong path, or requested item not found.|
| 429 | TOO_MANY_REQUESTS | Requests have exceeded rate limit.<br> In such case additional headers will be returned in the response. |
| 500 | INTERNAL_SERVER_ERROR | An internal error occurred. |

<br>

<em>Additional response headers per HTTP status:</em>

<br>


| HTTP Status | Header | Description |
| ----------- | ------ | ----------- |
| 429 | Retry-After | Time to wait (in seconds) until next allowed authorized request. |
| | X-RateLimit-Limit | Number of allowed authorized requests per second. |
| | X-RateLimit-Remaining | Number of remaining allowed authorized requests.
| | X-RateLimit-Reset |  The time (milliseconds since Epoch) in which another authorized request may be sent. |


<br>

### Errors

In case of an error HTTP status, other than 404, a response with the following attributes will be returned:  

| Name | Type | Description |
| ---- | ---- | ----------- |
| code | Number | As described in error codes table below. |
| message | String | An error messag, providing further details. |
| refId | String | A reference ID which can be used for contacting support. |

<br>

<em>Error codes</em>

| Code | Description |
| ---- | ----------- |
| 1xxx | General errors. |
| 2xxx | Authorization errors. |
| 2001 | Not enough permissions. |
| 2002 | Rate limit exceeded. |
| 3xxx | Validation errors. |

<em>Examples:</em>

```json
{
     "code": 1001,
     "message": "instrument_id has invalid value",
     "refIf": "225ae441-a018-4409-84b5-218c7c6186d1"
}
```

```json
{
     "code": 2001,
     "message": "rate limit reached",
     "refIf": "5aed6398-4cc4-4d2f-b977-adf4f4f7dedd"
}
```

<br>

<br>

## eToroX Authentication
<hr>


At eToroX we value the security of our clients very highly.<br>At each authenticated end-point, users need to verify their identity. This authentication process was created to ensure that no user’s identity can be stolen or forged.

> Do not share your private keys! eToroX will never ask you to share or send your private keys.


<br>

<br>

### Access to eToroX API
Access to eToroX API is only permitted for verified users.  Verified developer app users can manage their *developer app API tokens* from the website UI.

#### Developer APP

The `Developer app` enables access to the eToroX API.

Every `developer app` provides the following information:
* HTTPS API - A private DNS for https access.
* WebSockets - A private DNS for websocket access. The properties are accessible from the trading API settings section on the website

> Requests for developer apps are currently individually reviewed by Customer Support, and manually approved.

#### API tokens
`API tokens` are generated based on the `developer app`. Users can manage multiple `API tokens`, by identifying each `API token` with a customized token name.
When tokens are generated, the following information becomes accessible:
* API key - A UUID id of this token.
* Private Key - A private key used for signing.

> Important! This information cannot be retrieved and must be stored by the user

<br>

### Benifits of Authentication Process

* Access to API granted by creating specific `API tokens`.
* `API token` can only be created with 2FA verification.
* `API token` data only available during creation.
* `API token` credentials are comprised of an asymmetric RSA private/public pair of keys.
* During `API token` creation, the user  receives a private key,  to sign data.
* eToroX stores only the asymmetric public key. Even if a user has Read Access to our servers, this information is completely inaccessible, and user identities cannot be forged. 
* As part of the authentication process, eToroX mitigates "man-in-the-middle" attacks using a combination of TLS encryption, a timestamp and nonce (Number Only Used Once) validations with asymmetric signature signing.<br>*Note: Sensitive information should NEVER be passed in API communication.*
* As part of the authentication process, eToroX mitigates replay attacks by verifying both nonce and timestamp verifications.
* `API tokens` permissions can be scoped when created. Scoping ensures that a token is only used for specific actions.
* `API token` details are immutable. They can always be deleted, but never modified or retrieved.

<br>

### Authentication process

The authentication process requires the following parameters:
* API key - A UUID identifies this token
* Private key - An asymmetric RSA private key that should never be shared
* Timestamp - Number of milliseconds since epoch of the moment the signature was created (example: 1567334955567)
* Nonce - A UUID

eToroX servers verifies user identity based on the following:

* API key
* Signature For details about creating a signature, see the following section
* Nonce
* Timestamp

#### Create a signature
Asymmetric signatures are generated by the user to “prove” to the eToroX server that the user has access to their own private keys. eToroX then confirms this by running a similar but asymmetric process with the public key equivalence.
The following parameters are used for signature generation:
* API key
* Private key
* Nonce
* Milliseconds since epoch

##### Private key
The private keys generated by eToroX are:
* `PKCS8` encoded.
* `PEM` formatted.
* Cipher `AES-256-CBC`
* Empty string as the passphrase `` .

*Some libraries may require the user to convert the private key into different encoding. This can be done by using the `openssl` command line tool.*

##### Signing process

The payload the user signs is a concatenation of the `nonce` and `milliseconds since epoch`.
```js
const payload = `${nonce}${timestamp}`;
```

The payload should be signed with a `SHA256` signature algorithm and result should be `base64` encoded.

```js
const payload = `${nonce}${timestamp}`;
const signer = crypto.createSign('sha256');
signer.update(payload);
signature = signer.sign({key: privateKey , passphrase : ''}, 'base64');
```

#### Troubleshooting

* [Example of dummy signature process ](https://gist.github.com/etorox/457009e1e02bdf878225da09fdb64acf)
* [Convert pkcs8 to pkcs1 using openssl native](https://gist.github.com/etorox/094eb6d22cf83d72218d2d28ff304bc4)

<br>

<br>

## Public APIs
<hr>

<br>

### Get Server Time

```
GET /v1/time
```

Get current server timestamp (milliseconds since the Unix epoch).  

<br>

<em>Request parameters:</em>  
      
None  

<br>

<em>Response:</em>

| Name        | Type        | Description        |
| ----------- | ----------- | ------------------ |
| timestamp   | Number      | Current server timestamp (milliseconds since the Unix epoch). |  
  
<br>

<em>Example:</em>

```json
{
     "timestamp": "2021-02-16T13:45:47.229"
}
```

<br>

### Get Supported Currencies  

```
GET /v1/currencies
```
Provides information on all supported currencies.  

<br>

<em>Request parameters:</em>  
      
None  

<br>

<em>Response:</em>  
       
| Name        | Type        | Description |
| ----------- | ----------- | ----------- |
| id | String | The identifier of the currency. |
| name | String | The full name of the currency. |
| unifiedCryptoAssetId | Number | The currency’s unique numerical ID.<br>Only relevant for `coin` type currencies. |
| type | String | `coin`/`fiat` – Specifies if this currency is a fiat or crypto coin. |
| iconUrl | String | Relative path to retrieve the currency’s icon.<br>Base path is: resources.etorox.com |
| baseFactor | Number | Amount of "minor units" in a single currency unit (e.g. 100 cents per USD). |
| precision | Number | Maximum decimal places for current currency. |
| transactionUrlTemplate | String  | A template path to obtain transaction information.<br>Only relevant for `coin` type currencies. |
  
<br>

<em>Example:</em>

```json
[
  {
      "id": "bch",
      "name": "Bitcoin Cash",
      "unifiedCryptoassetId": 1831,
      "type": "coin",
      "iconUrl": "icons/currencies/bch_5578e57fc42fcc06.png",
      "baseFactor": 100000000,
      "precision": 4,
      "transactionUrlTemplate": "https://www.blocktrail.com/BCC/tx/#{txid}"
  },
  {
      "id": "bnb",
      "name": "Binance Coin",
      "unifiedCryptoassetId": 1839,
      "type": "coin",
      "iconUrl": "icons/currencies/bnb_a11a7d8eab82d907.png",
      "baseFactor": 100000000,
      "precision": 8,
      "transactionUrlTemplate": ""
  },
  {
      "id": "btc",
      "name": "Bitcoin",
      "unifiedCryptoassetId": 1,
      "type": "coin",
      "iconUrl": "icons/currencies/btc_8d46a67241b91050.png",
      "baseFactor": 100000000,
      "precision": 4,
      "transactionUrlTemplate": "https://www.blockchain.com/btc/tx/#{txid}"
  },
  {
      "id": "dash",
      "name": "Dash",
      "unifiedCryptoassetId": 131,
      "type": "coin",
      "iconUrl": "icons/currencies/dash_5eade54246c64e8a.png",
      "baseFactor": 100000000,
      "precision": 4,
      "transactionUrlTemplate": "https://live.blockcypher.com/dash/tx/#{txid}"
  },
  {
      "id": "etc",
      "name": "Ethereum Classic",
      "unifiedCryptoassetId": 1321,
      "type": "coin",
      "iconUrl": "icons/currencies/etc_dbcd63476bcdc94d.png",
      "baseFactor": 1000000000000000000,
      "precision": 8,
      "transactionUrlTemplate": "https://blockscout.com/etc/mainnet/tx/#{txid}"
  },
  {
      "id": "zec",
      "name": "Zcash ",
      "unifiedCryptoassetId": 1437,
      "type": "coin",
      "iconUrl": "icons/currencies/zec_921ce185c51d5023.png",
      "baseFactor": 100000000,
      "precision": 4,
      "transactionUrlTemplate": "https://explorer.zcha.in/transactions/#{txid}"
  }
]
```

<br>

### Get Trading Instruments  

```
GET /v1/instruments
```

Provides information on all instruments available for trading.  

<br>

<em>Request parameters:</em>        

None  

<br>

<em>Response:</em>  

| Name        | Type        | Description        |
| ----------- | ----------- | ------------------ |
| id | String | The identifier of the instrument. |  
| symbol | String | The symbol of the instrument, with separation between the currencies. |  
| type | String | crypto/fx – will be crypto in case of any crypto currency in the instrument. Otherwise, will be fx (forex). |  
| isCross | Boolean | Is this a direct trading pair, or a cross is required. |  
| baseCurrency | String | The id of the base currency of the instrument. |  
| quoteCurrency | String | The id of the quote currency of the instrument. |  
| baseCurrencyPrecision | Number | Maximal decimal places for base currency. |  
| quoteCurrencyPrecision | Number | Maximal decimal places for quote currency. |

<br>

<em>Example:</em>

```json
  [
    {
        "id": "adausd",
        "symbol": "ADA/USD",
        "type": "crypto",
        "isCross": false,
        "baseCurrency": "ada",
        "quoteCurrency": "usd",
        "baseCurrencyPrecision": 5,
        "quoteCurrencyPrecision": 5
    },
    {
        "id": "bchusd",
        "symbol": "BCH/USD",
        "type": "crypto",
        "isCross": false,
        "baseCurrency": "bch",
        "quoteCurrency": "usd",
        "baseCurrencyPrecision": 5,
        "quoteCurrencyPrecision": 5
    },
    {
        "id": "zecusd",
        "symbol": "ZEC/USD",
        "type": "crypto",
        "isCross": false,
        "baseCurrency": "zec",
        "quoteCurrency": "usd",
        "baseCurrencyPrecision": 5,
        "quoteCurrencyPrecision": 5
    }
  ]
```

<br>

### Get Order Book

```
GET /v1/instruments/{instrumentId}/depth
```

<br>

<em>Request parameters:</em>

| Name        | Type        | Mandatory | Default | Range | Parameter location | Description |
| ----------- | ----------- | --------- | ------- | ----- | ----------- | --------- |
| instrumentId | String     | Yes       |     N/A |       |  Path | The trading instrument for which to fetch the order book. |
| limit | Number | No | 20 | 1-300 | Query | Maximum number of levels to return. |

<br>

<em>Response:</em>

| Name | Type | Description |
| ---- | ---- | ---- |
| lastUpdatedAt | Number | The timestamp of the last order book update (milliseconds since the Unix epoch). |
| snapshotId | Number | The last update snapshot ID – used to correlate with the Websocket api. |
| bids | Array | Array of active bids. Each entry is an array of:<br> [0] – price (String).<br> [1] – volume (String). |
| asks | Array | Array of active asks. Each entry is an array of:<br> [0] - price (String).<br> [1] - volume (String). |

<br>

<em>Example:</em>

```json
  {
    "timestamp": "2021-02-16T14:12:40.654",
    "snapshotId": 27866022701522910,
    "asks": [
        [
            "698.34724",
            "24.8942"
        ],
        [
            "698.39",
            "4.9629"
        ],
        [
            "698.7436",
            "24.8169"
        ]
    ],
    "bids": [
        [
            "695.72",
            "5.0147"
        ],
        [
            "695.67161",
            "16.4742"
        ],
        [
            "695.25683",
            "19.2955"
        ]
    ]
}
```

<br>

### Get Time and Sales

```
GET /v1/instruments/{instrumentId}/timesales
```

<br>

<em>Request parameters:</em>

| Name        | Type        | Mandatory | Default | Range | Parameter location | Description |
| ----------- | ----------- | --------- | ------- | ----- | ------------------ | ----------- |
| instrumentId | String     | Yes       |     N/A |       |  Path | The trading instrument for which to fetch the time and sales. |
| limit | Number | No | 10 | 1-300 | Query | Maximum number of records to return. |
| startTime | Number | No | 48 hours from current timestamp |  | Query | Only returns trades executed after the given startTime. Returns at most 48 hours from the given timestamp. |

  
<br>

<em>Response:</em>

| Name | Type | Description |
| ---- | ---- | ----------- |
|  | Array |  Array of recent trades including:<br> [0] - timestamp (Number).<br> [1] - price (String).<br> [2] - volume (String). |

<br>

<em>Example:</em>

```json
[
    [
        "2021-02-16T14:23:03.823",
        "49140.25",
        "0.0129"
    ],
    [
        "2021-02-16T14:23:03.708",
        "49139.57",
        "0.0371"
    ],
    [
        "2021-02-16T14:22:58.758",
        "49052",
        "0.0576"
    ]
]
```

<br>

<br>

## Private APIs
<hr>


All private APIs require an authentication process (see XXX for details) and require additional headers in the request’s header.
Private APIs are subjected to a limiter, as described in the [Rate Limit](#rate-limit) section.
the following parameters in the request’s header:  

<br>

### Private API Headers

The following parameters should be included in the header of every private API request:  

| Name | Type | Description |
| ---- | ---- | ----------- |
| user-agent | String | The name of the application using the API. |
| x-request-id | String | Random UUID – acts as a unique identifier for the current request. |
| x-timestamp | Number | Request timestamp (milliseconds since the Unix epoch). |
| x-signature | String | Signature of the entire request. For instructions on creating this signature, see [Authentication](#eToroX-Authentication). |

<br>

### Create Order

```
POST /v1/orders
```

Place a new trading order.  

<br>

<em>Request parameters:</em>

| Name | Type | Mandatory? | Default | Parameter location | Description |
| ---- | ---- | ---------- | ------- | ------------------ | ----------- |
| clientOrderId | String | Yes | N/A | Body | A unique order ID assigned by the client. |
| instrumentId | String | Yes | N/A | Body | Instrument of trade. Obtained from "GET /v1/instruments" (e.g. btcusd). |
| side | String | Yes | N/A | Body | `buy`, `sell` |
| type | String | Yes | N/A | Body | `limit`, `market`, `stop`, `stopLimit` |
| timeInForce | String | Yes | N/A | Body | `gtd`, `gtc`, `ioc`, `fok` |
| price | String | Conditional | N/A | Body | Price per unit.<br>  Used for `limit` and `stopLimit` orders. |
| stopPrice | String | Conditional | N/A | Body | The price per unit, which will trigger the order.<br> Used for `stop` and `stopLimit` orders. |
| volume | String | Yes | N/A | Body | Order volume. |
| displayVolume | String | Conditional | N/A | Body | Order volume to display in the public order book.<br> Used to create an iceberg order.<br> Only valid with `limit` type orders. |
| expireTime | Number | Conditional | N/A | Body | Order expiration timestamp (milliseconds since the Unix epoch).<br> Used for gtd orders. |

<br>

<em>Mandatory parameters per order type:</em>

| Order type | Additional mandatory parameters | Parameter location |
| ---------- | ------------------------------- | ------------------ |  
| limit | `price` | Body |
| stop | `stopPrice` | Body |
| stopLimit | `price, stopPrice` | Body |

<br>

<em>Mandatory parameters per timeInForce:</em>

| Order timeInForce | Additional mandatory parameters | Parameter location |
| ----------------- | ------------------------------- | ------------------ |
| gtd | `expireTime` | Body |

<br>

<em>Response:</em>  

| Name | Type | Description |
| ---- | ---- | ----------- |
| orderId | String | The unique order ID assigned by eToroX. |
| clientOrderId |String | The unique order ID assigned by the client. |
| instrumentId | String |The trading instrument. |
| side | String | `buy`, `sell` |
| type | String | `limit`, `market`, `stop`, `stopLimit` |
| status | String | `new`, `canceled`, `filled`, `partial`, `expired` |
| price | String | Price per unit. |
| stopPrice | String | The original price per unit which was specified to trigger the order.<br>Only in case of `stop`/`stopLimit` order type. |
| volume | String | Original order volume. |
| displayVolume | String | Order volume to display in the public order book.<br>Only in case of `iceberg` orders. |
| remainingVolume | String | The remaining volume for trade.<br> Only while the order is in `partial` status. |
| executedVolume | String | The volume which has already been filled. |
| avgPrice | String | The average filled price. |
| timeInForce | String | `gtd`, `gtc`, `ioc`, `fok` |
| createdAt | Number | Order creation timestamp (milliseconds since the Unix epoch). |
| expireTime  | Number  | Order expiration timestamp (milliseconds since the Unix epoch).<br>Only for `gtd` orders. |

<br>

<em>Example:</em>

```json
{
  "orderId": "5010f4ba-4a4f-4976-9b92-7b0ec6870382",
  "clientOrderId": "8ab37440-70ff-11eb-b540-9556803f9427",
  "instrumentId": "btcusd",
  "side": "buy",
  "type": "limit",
  "status": "new",
  "price": "35000",
  "volume": "1",
  "remainingVolume": "1",
  "executedVolume": "0",
  "avgPrice": "0",
  "timeInForce": "gtc",
  "createdAt": "2021-02-17T09:07:53.021"
}
```

<br>

### Cancel a Single Order

```
DELETE /v1/orders/{clientOrderId}
```

Cancel a single order, according to its `clientOrderId`.  

<br>

<em>Additional request parameters:</em>

| Name | Type | Mandatory? | Default | Parameter location | Description |
| ---- | ---- | ---------- | ------- | ------------------ | ----------- |
| clientOrderId | String | Yes | N/A | Path | The `clientOrderId` of the order to cancel. |
| instrumentId | String | Yes |  N/A | Query |  The instrument ID of the order to cancel. |

<br>

<em>Response:</em>

| Name | Type | Description |
| ---- | ---- | ----------- |
| orderId | String | The unique order ID assigned by eToroX. |
| clientOrderId | String | The unique order ID assigned by the client. |
| instrumentId | String | The trading instrument.
| side | String | `buy`, `sell` |
| type | String | `limit`, `market`, `stop`, `stopLimit` |
| status | String | `new`, `canceled`, `filled`, `partial`, `expired` |
| price | String | Price per unit. |
| stopPrice | String |The original price per unit which was specified to trigger the order.<br>Only in case of `stop`/`stopLimit` order type.|
| volume | String | Original order volume.
| displayVolume | String | Order volume to display in the public order book.<br>Only in case of `iceberg` orders. |
| remainingVolume | String | The remaining volume for trade.<br>Only while the order is in `partial` status. |
| executedVolume | String | The volume which has already been filled. |
| avgPrice | String | The average filled price. |
| timeInForce | String | `gtd`, `gtc`, `ioc`, `fok` |
| createdAt | Number | Order creation timestamp (milliseconds since the Unix epoch). |
| expireTime | Number | Order expiration timestamp (milliseconds since the Unix epoch).<br>Only for `gtd` orders. |

<br>

<em>Example:</em>

```json
{
  "orderId": "5010f4ba-4a4f-4976-9b92-7b0ec6870382",
  "clientOrderId": "ff85fc0d-495c-42c1-b427-443acf46ef08",
  "instrumentId": "btcusd",
  "side": "buy",
  "type": "limit",
  "status": "canceled",
  "price": "35000",
  "volume": "1",
  "remainingVolume": "0",
  "executedVolume": "0",
  "avgPrice": "0",
  "timeInForce": "gtc",
  "createdAt": "2021-02-17T09:17:32.659"
}
```

<br>

### Cancel Multiple Orders

```
DELETE /v1/orders
```

Cancel all open orders matching the provided `instrumentId`(s). In case no `instrumentId` is provided, cancel all open orders.  

<br>

<em>Additional request parameters:</em>

| Name | Type | Mandatory? | Default | Parameter location | Description |
| ---- | ---- | ---------- | ------- | ------------------ | ----------- |
| instrumentId | String | No | All | Query | Cancels all open orders on the given instrument ID.<br>Multiple values may be specified, separated by comma.<br>If none are specified, cancels all open orders |

<br>

<em>Response:</em>

An array including the following attributes for each canceled order:  

| Name | Type | Description |
| ---- | ---- | ----------- |
| orderId |  String | The unique order ID assigned by eToroX. |
| clientOrderId | String | The unique order ID assigned by the client. |
| instrumentId | String | The trading instrument. |
| side | String | `buy`, `sell` |
| type | String | `limit`, `market`, `stop`, `stopLimit` |
| status | String | `new`, `canceled`, `filled`, `partial`, `expired` |
| price | String | Price per unit. |
| stopPrice | String | The original price per unit which was specified to trigger the order.<br>Only in case of `stop`/`stopLimit` order type. |
| volume | String | Original order volume. |
| displayVolume | String | Order volume to display in the public order book.<br> Only in case of `iceberg` orders. |
| remainingVolume | String | The remaining volume for trade.<br>Only while the order is in `partial` status. |
| executedVolume | String | The volume which has already been filled. |
| avgPrice | String | The average filled price. |
| timeInForce | String | `gtd`, `gtc`, `ioc`, `fok` | 
| createdAt |  Number | Order creation timestamp (milliseconds since the Unix epoch). |
| expireTime | Number | Order expiration timestamp (milliseconds since the Unix epoch).<br>Only for `gtd` orders. |

<br>

<em>Example:</em>

```json
[
  {
    "orderId": "42bcb301-8d9b-4868-960c-301fd8af7477",
    "clientOrderId": "d865cba7-504b-4288-b657-39254f50e2fa",
    "instrumentId": "btcusd",
    "side": "buy",
    "type": "limit",
    "status": "canceled",
    "price": "43000",
    "volume": "1",
    "remainingVolume": "0",
    "executedVolume": "0",
    "avgPrice": "0",
    "timeInForce": "gtc",
    "createdAt": "2021-02-17T09:33:13.992"
  },
  {
    "orderId": "7ad0c76b-198a-4e01-b5a3-979f64208b55",
    "clientOrderId": "d865cba7-504b-4288-b657-39254f50e2fa",
    "instrumentId": "btcusd",
    "side": "buy",
    "type": "limit",
    "status": "canceled",
    "price": "37000",
    "volume": "2",
    "remainingVolume": "0",
    "executedVolume": "0",
    "avgPrice": "0",
    "timeInForce": "gtd",
    "createdAt": "2021-02-17T09:33:13.998",
    "expireTime": "2021-02-18T01:00:00.000"
  }
]
```

<br>

### Get Single Order Details

```
GET /v1/orders/{clientOrderId}
```

Get details of a specific order, according to its `clientOrderId`.  

<br>

<em>Additional request parameters:</em>

| Name | Type | Mandatory? | Default | Parameter location | Description |
| ---- | ---- | ---------- | ------- | ------------------ | ----------- |
| clientOrderId | String | Yes | N/A | Path | The `clientOrderId` of the requested order. |
| instrumentId | String | Yes | N/A | Query | The instrument ID of the requested order. |

<br>

<em>Response:</em>

| Name | Type | Description |
| ---- | ---- | ----------- |
| orderId | String | The unique order ID assigned by eToroX. |
| clientOrderId | String | The unique order ID assigned by the client. |
| instrumentId  | String | The trading instrument. |
| side | String | `buy`, `sell` |
| type | String | `limit`, `market`, `stop`, `stopLimit` |
| status | String | `new`, `canceled`, `filled`, `partial`, `expired` |
| price | String | Price per unit. |
| stopPrice | String | The original price per unit which was specified to trigger the order.<br>Only in case of `stop`/`stopLimit` order type. |
| volume | String | Original order volume. |
| displayVolume | String | Order volume to display in the public order book.<br>Only in case of `iceberg` orders. |
| remainingVolume | String | The remaining volume for trade.<br>Only while the order is in `partial` status. |
| executedVolume | String | The volume which has already been filled. |
| avgPrice | String | The average filled price. |
| timeInForce | String | `gtd`, `gtc`, `ioc`, `fok` |
| createdAt | Number | Order creation timestamp (milliseconds since the Unix epoch). |
| expireTime | Number | Order expiration timestamp (milliseconds since the Unix epoch).<br>Only for `gtd` orders. |

<br>

<em>Example:</em>

```json
{
    "orderId": "bf7e3fd8-92c9-415f-b667-01a917eeb6b0",
    "clientOrderId": "712ba400-7106-11eb-89d4-b5278d445e1d",
    "instrumentId": "btcusd",
    "side": "buy",
    "type": "limit",
    "status": "filled",
    "price": "44208",
    "volume": "1",
    "remainingVolume": "0",
    "executedVolume": "1",
    "avgPrice": "44208",
    "timeInForce": "gtc",
    "createdAt": "2021-02-17T09:57:01.472"
}
```

<br>

### Get Multiple Orders Details

```
GET /v1/orders
```

Get all account’s orders, matching the given parameters.<br>Results are ordered by creation time (`createdAt` field) in a descending order.  

<br>

<em>Additional request parameters:</em>

| Name | Type | Mandatory? | Default | Range | Parameter location | Description |
| ---- | ---- | ---------- | ------- | ----- | ------------------ | ----------- |
| status | String | No | `new`, `partial` | | Query | Only returns orders in the given status.<br>Possible values: `new`, `canceled`, `filled`, `partial`, `expired`.<br>If not specified, returns all open orders. |
| instrumentId | String | No | All | | Query | If specified, only returns orders for the given instrument ID. Otherwise, returns orders from all instruments. |
| afterId | String | No | None | | Query | If specified, only returns orders created after the provided `clientOrderId`. |
| startTime | Number | No | 48 hours from current timestamp | | Query | Only returns orders created after the provided `startTime`. |
| endTime | Number | No | 48 hours from startTime | Up to 48 hours from startTime | Query | Only returns orders created up to the provided endTime. |
| limit | Number | No | 25 | 1-300 | Query | Maximum number of orders to return. |

<br>

<em>Response:</em>

An array including the following attributes for each matching order:  

| Name | Type | Description |
| ---- | ---- | ----------- |
| orderId | String | The unique order ID assigned by eToroX. |
| clientOrderId | String | The unique order ID assigned by the client. |
| instrumentId  | String | The trading instrument. |
| side | String | `buy`, `sell` |
| type | String | `limit`, `market`, `stop`, `stopLimit` |
| status | String | `new`, `canceled`, `filled`, `partial`, `expired` |
| price | String | Price per unit. |
| stopPrice | String | The original price per unit which was specified to trigger the order.<br>Only in case of `stop`/`stopLimit` order type. |
| volume | String | Original order volume. |
| displayVolume | String | Order volume to display in the public order book.<br>Only in case of `iceberg` orders. |
| remainingVolume | String | The remaining volume for trade.<br>Only while the order is in `partial` status. |
| executedVolume | String | The volume which has already been filled. |
| avgPrice | String | The average filled price. |
| timeInForce | String | `gtd`, `gtc`, `ioc`, `fok` |
| createdAt | Number | Order creation timestamp (milliseconds since the Unix epoch). |
| expireTime | Number | Order expiration timestamp (milliseconds since the Unix epoch).<br>Only for `gtd` orders. |

<br>

<em>Example:</em>

```json
[
  {
    "orderId": "cf9ff685-119b-4f03-9e36-6179beb580bd",
    "clientOrderId": "7f75a150-7106-11eb-89d4-b5278d445e1d",
    "instrumentId": "ethbtc",
    "side": "buy",
    "type": "limit",
    "status": "new",
    "price": "0.027",
    "volume": "1",
    "remainingVolume": "1",
    "executedVolume": "0",
    "avgPrice": "0",
    "timeInForce": "gtc",
    "createdAt": "2021-02-17T09:57:20.314"
  },
  {
    "orderId": "bf7e3fd8-92c9-415f-b667-01a917eeb6b0",
    "clientOrderId": "712ba400-7106-11eb-89d4-b5278d445e1d",
    "instrumentId": "btcusd",
    "side": "buy",
    "type": "limit",
    "status": "new",
    "price": "44208",
    "volume": "1",
    "remainingVolume": "1",
    "executedVolume": "0",
    "avgPrice": "0",
    "timeInForce": "gtc",
    "createdAt": "2021-02-17T09:57:01.472"
  }
]
```

<br>

### Get List of Trades

```
GET /v1/trades
```

Get details of executed trades.<br>Results are ordered by execution time (`createdAt` field) in a descending order.  

<br>

<em>Additional request parameters:</em>

| Name | Type | Mandatory? | Default | Range | Parameter location | Description |
| ---- | ---- | ---------- | ------- | ----- | ------------------ | ----------- |
| clientOrderId | String | No | All |  | Query | If specified, only returns trades created from the given `clientOrderId`. Otherwise, returns trades from all matching orders. |
| instrumentId | String | No | All |  | Query | If specified, only returns trades for the given instrument ID. Otherwise returns trades from all instruments. |
| afterId | String | No | None |  | Query | If specified, only return trades which occurred after the provided `tradeId`. |
| startTime | Number | No | 48 hours from current timestamp |  | Query | Only returns trades executed after the provided `startTime`. |
| endTime | Number | No | 48 hours from `startTime` | Up to 48 hours from `startTime` | Query Only returns trades executed up to the provided endTime. |
| limit | Number | No | 25 | 1-300 | Query | Maximum number of trades to return. |

<br>

<em>Response:</em>

An array including the following attributes for each matching trade:  

| Name | Type | Description |
| ---- | ---- | ----------- |
| tradeId | String | The unique trade ID assigned by eToroX. |
| clientOrderId | String | The unique ID assigned by the client to the order from which the trade was created. |
| instrumentId | String | The trading instrument. |
| orderId | String | The unique id assigned by eToroX’s to the order from which this trade was created (equivalent to field `orderId` in "GET /v1/orders"). |
| side | String | `buy`, `sell` |
| price | String | Executed trade price. |
| volume | String | Executed trade volume. |
| commission | String | Amount of commission paid for this trade. The commission is taken from the received amount. |
| commisionCurrency | String | The currency in which the commission was paid. |
| executionType | String | `maker`, `taker` |
| partyId | String | The identifier of the counterparty of the trade. |
| createdAt | Number | Trade timestamp (milliseconds since the Unix epoch). |

<br>

<em>Example:</em>

```json
[
  {
    "tradeId": "f0697b72-7115-11eb-85a1-42010a3e0035",
    "orderId": "bf7e3fd8-92c9-415f-b667-01a917eeb6b0",
    "clientOrderId": "712ba400-7106-11eb-89d4-b5278d445e1d",
    "instrumentId": "btcusd",
    "side": "buy",
    "price": "44208",
    "volume": "0.16",
    "commission": "0",
    "commissionCurrency": "btc",
    "executionType": "maker",
    "partyId": "d019543c0737a488fbbf9949afce04eb",
    "createdAt": "2021-02-17T11:47:40.662"
  },
  {
    "tradeId": "f0074a22-7115-11eb-85a1-42010a3e0035",
    "orderId": "bf7e3fd8-92c9-415f-b667-01a917eeb6b0",
    "clientOrderId": "712ba400-7106-11eb-89d4-b5278d445e1d",
    "instrumentId": "btcusd",
    "side": "buy",
    "price": "44208",
    "volume": "0.18",
    "commission": "0",
    "commissionCurrency": "btc",
    "executionType": "maker",
    "partyId": "d019543c0737a488fbbf9949afce04eb",
    "createdAt": "2021-02-17T11:47:40.019"
  },
  {
    "tradeId": "efe8758c-7115-11eb-85a1-42010a3e0035",
    "orderId": "bf7e3fd8-92c9-415f-b667-01a917eeb6b0",
    "clientOrderId": "712ba400-7106-11eb-89d4-b5278d445e1d",
    "instrumentId": "btcusd",
    "side": "buy",
    "price": "44208",
    "volume": "0.18",
    "commission": "0",
    "commissionCurrency": "btc",
    "executionType": "maker",
    "partyId": "d019543c0737a488fbbf9949afce04eb",
    "createdAt": "2021-02-17T11:47:39.817"
  }
]
```

<br>

### Get Portfolio Status

```
GET /v1/portfolio
```

<br>

<em>Additional request parameters:</em>

None  

<br>

<em>Response:</em>

**Note:** the conversion to USD is calculated according to an internal conversion rate, as determined at `lastUpdatedAt` time.  

| Name | Type | Description |
| ---- | ---- | ----------- |
| lastUpdatedAt | Number | The timestamp of the last portfolio update (milliseconds since the Unix epoch). |
| totalUsdBalance | String | Total balance, converted to USD. **Optional. For credited customers** |
| totalUsdCredit | String | Total credit, converted to USD. **Optional. For credited customers** |
| marginLevel | String | Current margin level calculated by:<br>`totalUsdBalance` / `totalUsdBalance` + `totalUsdCredit`. **Optional. For credited customers**|
| marginCallUsdBalance | String | The `totalUsdBalance` at which a margin call will be triggered. **Optional. For credited customers**|
| liquidationUsdBalance | String | The `totalUsdBalance` at which eToroX will begin to automatically close open positions. **Optional. For credited customers**|
| currency | Array | An array describing the user’s portfolio status for each currency.<br>Each entry contains the attributes described in the next table. |

<br>

<em>Attributes per currency:</em>

| Name | Type | Description |
| ---- | ---- | ----------- |
| currency | String | The currency id – as it appears in "GET /v1/currencies". |
| available | String | Available funds for trading, calculated by: `balance` - `locked`. |
| locked | String | Amount of funds currently locked in open orders or pending withdrawals. |
| balance | String | Total funds, calculated by: `available` + `locked`. |
| credit | String | Amount of credit received from eToroX. |
| marginImpact | String | The multiplier used when calculating the impact of the current currency on the `totalUsdBalance`.<br>Each currency’s contribution is calculated by: `balance` * `marginImpact` (converted to USD). |

<br>

<em>Example:</em>

```json
{
  "lastUpdatedAt": "2021-02-17T09:44:26.712",
  "totalUsdBalance": "2153296108.4200",
  "totalUsdCredit": "55717.3000",
  "marginLevel": "0.999974",
  "marginCallUsdBalance": "???",
  "liquidationUsdBalance": "???",
  "currencies": [
    {
      "currency": "btc",
      "available": "2985.907",
      "balance": "3784.997",
      "credit": "1",
      "locked": "800.09",
      "marginImpact": "1"
    },
    {
      "currency": "eos",
      "available": "20",
      "balance": "20",
      "credit": "0",
      "locked": "0",
      "marginImpact": "0"
    },
    {
      "currency": "eth",
      "available": "100",
      "balance": "100",
      "credit": "0",
      "locked": "0",
      "marginImpact": "1"
    }
  ]
}
```
