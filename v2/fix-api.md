<br>

<br>

# eToroX API Documentation 3rd revision
<br>

<br>

<br>

## eToroX FIX API

<hr>

The FIX API protocol, with its unsurpassed capability to reach a speed of up to 100,000 orders per second, is the default API protocol within eToroX’s professional product suite.<br>Positioned as the world standard in the traditional finance arena for professional trading, FIX API enables faster and smoother integration and authentication, thereby reducing friction and costs.

<br>

### Supported messages

Standard message FIX headers and trailers should be applied to messages:

<em>Standard Header:</em>

| Tag | Field name      | Required | Type         | Comments                                                                                                                                                                             |
|-----|-----------------|----------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 8   | BeginString     | Y        | String       | FIX.4.4 (Always unencrypted, must be first field in message)                                                                                                                         |
| 9   | BodyLength      | Y        | Length       | Message length, in bytes, forward to the CheckSum field. Always unencrypted, must be second field in message.                                                                        |
| 35  | MsgType         | Y        | String       | Defines message type. Always unencrypted, must be third field in message.                                                                                                            |
| 49  | SenderCompID    | Y        | String       | Assigned value used to identify firm sending message. Always unencrypted.                                                                                                            |
| 56  | TargetCompID    | Y        | String       | Assigned value used to identify receiving firm. Always unencrypted.                                                                                                                  |
| 34  | MsgSeqNum       | Y        | SeqNum       | Integer message sequence number.                                                                                                                                                     |
| 43  | PossDupFlag     | N        | Boolean      | Indicates possible retransmission of message with this sequence number.<br>Required for retransmitted messages. Supported values:<br>‘Y’ (Possible duplicate)<br>'N' (Original transmission) |
| 97  | PossResend      | N        | Boolean      | Indicates that message may contain information that has been sent under another sequence number. Supported values:<br>‘Y’ (Possible resend)<br>'N' (Original transmission)           |
| 52  | SendingTime     | Y        | UTCTimestamp | Time of message transmission (expressed in UTC). YYYYMMDD-HH:MM:SS.sss                                                                                                               |
| 122 | OrigSendingTime | N        | UTCTimestamp | Original time of message transmission when transmitting messages as the result of resend request (expressed in UTC). Required for message resent as a result of a resend request.    |

<em>Standard Trailer:</em>

| Tag | Field name | Required | Type   | Comments                                                                       |
|-----|------------|----------|--------|--------------------------------------------------------------------------------|
| 10  | CheckSum   | Y        | String | Three byte, simple checksum. Always unencrypted, always last field in message. |

<br>

### FIX Session Level Messages

<em>Logon (MsgType = ‘A’)</em>

| Tag | Field name      | Required | Type    | Comments                                                                                                                             |
|-----|-----------------|----------|---------|--------------------------------------------------------------------------------------------------------------------------------------|
| 98  | EncryptMethod   | Y        | int     | Method of encryption. Supported values: '0' — None                                                                                   |
| 108 | HeartBtInt      | Y        | int     | Heartbeat interval (seconds)                                                                                                         |
| 141 | ResetSeqNumFlag | N        | Boolean | Indicates that the both sides of the FIX session should reset sequence numbers.                                                      |
| 554 | Password        | Y        | String  | The password is a concatenation of the generated fields according to Authentication Password format: {signature}_{nonce}_{timestamp} |

<em>Heartbeat (MsgType = ‘0’)</em>


| Tag | Field name | Required | Type   | Comments                                                                                   |
|-----|------------|----------|--------|--------------------------------------------------------------------------------------------|
| 112 | TestReqID  | N        | String | Identifier included in Test Request (1) message to be returned in resulting Heartbeat (0). |


<em>Test Request (MsgType = ‘1’)</em>

| Tag | Field name | Required | Type   | Comments                                                                                   |
|-----|------------|----------|--------|--------------------------------------------------------------------------------------------|
| 112 | TestReqID  | Y        | String | Identifier included in Test Request (1) message to be returned in resulting Heartbeat (0). |


<em>Resend Request (MsgType = ‘2’)</em>

| Tag | Field name | Required | Type   | Comments |
|-----|------------|----------|--------|--------- |
| 7   | BeginSeqNo | Y        | SeqNum | Message sequence number of first message in range to be resent.  |
| 16  | EndSeqNo   | Y        | SeqNum | Message sequence number of last message in range to be resent.<br>If request is for a single message BeginSeqNo (7) = EndSeqNo (16).<br>If request is for all messages subsequent to a particular message, EndSeqNo (16) = '0' (representing infinity). |


<em>Session Level Reject (MsgType = ‘3’)</em>

| Tag | Field name          | Required | Type       | Comments |
|-----|---------------------|----------|------------|----------|
| 45  | RefSeqNum           | Y        | SeqNum     | MsgSeqNum (34) of rejected message.  |
| 371 | RefTagID            | N        | int        | The tag number of the FIX field being referenced. |
| 372 | RefMsgType          | N        | String(10) | The MsgType (35) of the FIX message being referenced.  |
| 373 | SessionRejectReason | N        | int        | Code to identify reason for reject. See supported values in a table below. |
| 58  | Text                | N        | String     | Message to explain reason for rejection. |

<em>SessionRejectReason values:</em>

| Code | Reason |
| ---- | ------ |
| 0  | Invalid tag number |
| 1  | Required tag missing |
| 2  | Tag not defined for this message type |
| 3  | Undefined tag |
| 4  | Tag specified without a value |
| 5  | Value is incorrect (out of range) for this tag |
| 6  | Incorrect data format for value) |
| 7  | Decryption problem |
| 8  | Signature problem |
| 9  | CompID problem |
| 10 | SendingTime accuracy problem |
| 11 | Invalid MsgType (35) |
| 13 | Tag appears more than once |
| 14 | Tag specified out of required order |
| 15 | Repeating group fields out of order |
| 16 | Incorrect NumInGroup count for repeating group |
| 99 | Other |

<em>Reset Sequence (MsgType = ‘4’)</em>

| Tag | Field name  | Required | Type    | Comments |
|-----|-------------|----------|---------|----------|
| 123 | GapFillFlag | N        | Boolean | Indicates that the Sequence Reset (4) message is replacing administrative or application messages which will not be resent. Supported values:<br>‘Y' (Gap Fill message, MsgSeqNum field valid)<br>'N' (Sequence Reset, ignore MsgSeqNum) |
| 36  | NewSeqNo    | Y        | SeqNum  | New sequence number  |


<em>Logout (MsgType = ‘5’)</em>


| Tag | Field name | Required | Type   | Comments      |
|-----|------------|----------|--------|---------------|
| 58  | Text       | N        | String | Logout reason |


<br>

### Market Data

<em>Market Data Request (MsgType = ‘V’)</em>

| Tag   | Field name              | Required | Type       | Comments                                                                                                                                    |
|-------|-------------------------|----------|------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| 262   | MDReqID                 | Y        | String     | Unique identifier for Market Data Request (V) String length 1-20                                                                            |
| 263   | SubscriptionRequestType | Y        | char       | Subscription Request Type Supported values:<br>1 = Snapshot + Updates (Subscribe)<br>2 = Disable previous Snapshot + Update Request (Unsubscribe) |
| 264   | MarketDepth             | Y        | int        | Depth of market for Book Snapshot Supported values:<br>0 - Full Book (up to 20 price levels)<br>1 - Top of book                             |
| 267   | NoMDEntryTypes          | Y        | NumInGroup | Number of MDEntryType (269) fields requested                                                                                                |
| =>269 | MDEntryType             | Y        | char       | Type Market Data entry. Supported values:<br>0 – Bid<br>1 – Offer<br>2 – Trade                                                              |
| 146   | NoRelatedSym            | Y        | NumInGroup | Number of symbols (instruments) requested. Valid Values: 1                                                                                  |
| =>55  | Symbol                  | Y        | String     | Ticker symbol Supported values only single of: All symbols supported by eToroX in format like BTC/ETH                                       |

<em>Market Data – Full Refresh (MsgType = ‘W’)</em>

| Tag   | Field name  | Required | Type       | Comments                                                                                |
|-------|-------------|----------|------------|-----------------------------------------------------------------------------------------|
| 262   | MDReqID     | Y        | String     | Unique identifier for Market Data Request (V) this message is sent in reply to          |
| 55    | Symbol      | Y        | String     | Ticker symbol. Supported values: All symbols supported by eToroX in format like BTC/ETH |
| 268   | NoMDEntries | Y        | NumInGroup | Number of entries following.                                                            |
| =>269 | MDEntryType | Y        | char       | Type of Market Data entry. Valid values:<br>0 – Bid<br>1 – Offer                        |
| =>270 | MDEntryPx   | Y        | Price      | Price of the Market Data Entry.                                                         |
| =>271 | MDEntrySize | Y        | Qty        | Quantity or volume represented by the Market Data Entry.                                |

<em>Market Data – Incremental Refresh (MsgType = ‘X’)</em>

| Tag   | Field name     | Required | Type       | Comments                                                                                |
|-------|----------------|----------|------------|-----------------------------------------------------------------------------------------|
| 262   | MDReqID        | Y        | String     | Unique identifier for Market Data Request (V) this message is sent in reply to          |
| 268   | NoMDEntries    | Y        | NumInGroup | Number of entries following.                                                            |
| =>279 | MDUpdateAction | Y        | char       | Type of Market Data update action. Valid values:<br>0 – New<br>1 - Change<br>2 - Delete |
| =>269 | MDEntryType    | Y        | char       | Type of Market Data entry. Valid values:<br>0 - Bid<br>1 - Offer<br>2 – Trade           |
| =>55  | Symbol         | Y        | String     | Ticker symbol. Supported values: All symbols supported by eToroX in format like BTC/ETH |
| =>270 | MDEntryPx      | Y        | Price      | Price of the Market Data Entry.                                                         |
| =>271 | MDEntrySize    | Y        | Qty        | Quantity or volume represented by the Market Data Entry.                                |

<em>Market Data Request Reject (MsgType = ‘Y’)</em>

| Tag | Field name     | Required | Type   | Comments                                                                                 |
|-----|----------------|----------|--------|------------------------------------------------------------------------------------------|
| 262 | MDReqID        | Y        | String | Must refer to the MDReqID (262) of the request.                                          |
| 281 | MDReqRejReason | Y*       | char   | Reason for the rejection of a Market Data Request (V). See supported values in a table below. |
| 58  | Text           | N        | String | Extra explanations on rejection reason                                                   |

<em> MDReqRejReason values:</em>

| Code | Reason |
| ---- | ------ |
| '0' | Unknown symbol |
| '1' | Duplicate MDReqID (262) |
| '4' | Unsupported SubscriptionRequestType (263) |
| '5' | Unsupported MarketDepth (264) |
| '8' | Unsupported MDEntryType (269) |

<br>

### Trading

<em>New Order – Single (MsgType = ‘D’)</em>

| Tag | Field name   | Required | Type         | Comments                                                                                                                                            |
|-----|--------------|----------|--------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| 11  | ClOrdID      | Y        | String       | Unique identifier of the order as assigned by the client                                                                                            |
| 55  | Symbol       | Y        | String       | Ticker symbol. Valid values: All symbols supported by eToroX                                                                                        |
| 54  | Side         | Y        | char         | Side of order. Valid values:<br>'1' - Buy<br>'2' - Sell                                                                                             |
| 60  | TransactTime | Y        | UTCTimestamp | Time of order creation.                                                                                                                             |
| 38  | OrderQty     | Y        | Qty          | Quantity ordered                                                                                                                                    |
| 40  | OrdType      | Y        | char         | Order type. Valid values:<br>'1' - Market<br>'2' - Limit                                                                                            |
| 44  | Price        | C        | Price        | Price of the limit order. Required for OrderType(40) = ‘2’ (Limit)                                                                                  |
| 59  | TimeInForce  | Y        | char         | Specifies how long the order remains in effect. Valid values:<br>'1' - Good Till Cancel (GTC)<br>'3' - Immediate Or Cancel (IOC)<br>'6' - Good Till Date (GTD) |
| 126 | ExpireTime   | C        | UTCTimestamp | Conditionally required if TimeInForce <59> = GTD                                                                                                    |

<em>Order Cancel Request (MsgType = ‘F’)</em>

| Tag | Field name   | Required | Type         | Comments                                                                                          |
|-----|--------------|----------|--------------|---------------------------------------------------------------------------------------------------|
| 41  | OrigClOrdID  | Y        | String       | ClOrdID (11) of the previous non-rejected order when canceling an order. Used for reference only. |
| 11  | ClOrdID      | Y        | String       | Unique ID of cancel request as assigned by the client                                             |
| 60  | TransactTime | Y        | UTCTimestamp | Time of order creation                                                                            |
| 54  | Side         | Y        | Char         | Side of order                                                                                     |

<em>Order Cancel Reject (MsgType = ‘9’)</em>

| Tag | Field name       | Required | Type         | Comments                                                                                                                       |
|-----|------------------|----------|--------------|--------------------------------------------------------------------------------------------------------------------------------|
| 37  | OrderID          | Y        | String       | Unique identifier of the order in eToroX Trading System.                                                                       |
| 11  | ClOrdID          | Y        | String       | Unique ID of cancel request as assigned by the client                                                                          |
| 41  | OrigClOrdID      | Y        | String       | ClOrdID (11) of the previous non-rejected order when canceling an order.                                                       |
| 39  | OrdStatus        | Y        | char         | Identifies current status of order.                                                                                            |
| 434 | CxlRejResponseTo | Y        | char         | Identifies the type of request that an Order Cancel Reject (9) is in response to. Valid values: '1' - Order Cancel Request (F) |
| 60  | TransactTime     | N        | UTCTimestamp | Time of order creation.                                                                                                        |
| 102 | CxlRejReason     | N        | int          | Code to identify reason for cancel rejection. Valid values:<br>'1' - Unknown order<br>'99' - Other                                   |
| 58  | Text             | N        | String       | Free format text string – the reason for the reject.                                                                           |

<em>Execution Report (MsgType = ‘8’)</em>

| Tag | Field name   | Required | Type         | Comments                                                                                                                                   |
|-----|--------------|----------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 11  | ClOrdID      | Y        | String       | Unique identifier of the order or cancel request, this Execution Report relates to, as assigned by the client                              |
| 37  | OrderID      | Y        | String       | Unique identifier of the order assigned by eToroX Trading System.                                                                          |
| 41  | OrigClOrdID  | C        | String       | ClOrdID (11) of the cancelled order. Conditionally required for response to Cancel request (ExecType (150) = Canceled).                    |
| 17  | ExecID       | Y        | String       | Unique identifier of Execution Report (8) message as assigned by eToroX                                                                    |
| 150 | ExecType     | Y        | char         | Describes the specific Execution Report (8) type. Valid values:<br>'0' - New<br>'4' - Canceled<br>'8' - Rejected<br>'F' - Trade (partial fill or fill) |
| 39  | OrdStatus    | Y        | char         | Identifies current status of order. Valid values:<br>'0' - New<br>'1' - Partially filled<br>'2' - Filled<br>'4' - Canceled<br>'8' - Rejected              |
| 55  | Symbol       | Y        | String       | Ticker symbol Supported values: All symbols supported by eToroX in format like BTC/ETH                                                     |
| 54  | Side         | Y        | char         | Side of order. Valid values:<br>'1' - Buy<br>'2' - Sell                                                                                          |
| 40  | OrdType      | N        | char         | Order type. Valid values:<br>'1' - Market<br>'2' - Limit                                                                                         |
| 44  | Price        | C        | Price        | Echoed back from NOS (Limit only)                                                                                                          |
| 32  | LastQty      | С        | Qty          | Quantity bought/sold on this fill. Required if ExecType (150) = ‘F’ (Trade). LastQty not required when ExecType=Order Status               |
| 31  | LastPx       | С        | Price        | Price of this fill. Required if ExecType (150) = ‘F’ (Trade).                                                                              |
| 151 | LeavesQty    | Y        | Qty          | Quantity open for further execution. Always 0 for ExecType=’8’ (Rejected).                                                                 |
| 14  | CumQty       | Y        | Qty          | Currently quantity for the order. Always 0 for ExecType=’8’ (Rejected).                                                                    |
| 6   | AvgPx        | Y        | Price        | Average of all fills for this order (0 if New)                                                                                             |
| 60  | TransactTime | N        | UTCTimestamp | Time of order creation.                                                                                                                    |
| 58  | Text         | N        | String       | Free text for example rejection reason                                                                                                     |

<em>Business Message Reject (MsgType = ‘j’)</em>

| Tag | Field name           | Required | Type   | Comments                                                                                                                                                                                                                                                            |
|-----|----------------------|----------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 45  | RefSeqNum            | N        | SeqNum | Reference message sequence number                                                                                                                                                                                                                                   |
| 372 | RefMsgType           | Y        | String | The MsgType (35) of the FIX message being referenced.                                                                                                                                                                                                               |
| 379 | BusinessRejectRefID  | N        | String | The value of the business-level "ID" field on the message being referenced.                                                                                                                                                                                         |
| 380 | BusinessRejectReason | Y        | int    | Code to identify reason for a Business Message Reject (j) message. See supported values in a table below. |
| 58  | Text                 | N        | String | Free format text string – the reason for the reject.                                                                                                                                                                                                                |

<em>BusinessRejectReason values:</em>

| Code | Reason |
| ---- | ------ |
| 0 | Other |
| 1 | Unknown ID |
| 2 | Unknown Security |
| 3 | Unsupported Message Type |
| 4 | Application not available |
| 5 | Conditionally Required Field Missing |
| 6 | Not authorized |

<em>Order Status Request (MsgType = H)</em>

Sent by the client to obtain information about pending and done orders.<br>The response to an Order Status Request is an ExecutionReport with ExecType=I.<br>The ExecutionReport will contain the ClOrdID if the value is supplied.<br>If the order cannot be found, the ExecutionReport will have OrderID=0.

| Tag | Field name | Required | Type   | Comments                                                                                    |
|-----|------------|----------|--------|---------------------------------------------------------------------------------------------|
| 11  | ClOrdID    | Y        | String | ClOrdID of order requested. When supplying this value, you do not need to supply an OrderID |
| 54  | Side       | Y        | Char   | Side of order                                                                               |

<br>

### Authentication

As part of Logon (MsgType = `A`) field Password (554) should include authentication to verify the identity. This authentication process was created to ensure that no user’s identity can be stolen or forged.

> Do not share your private keys! eToroX will never ask you to share or send your private keys.

Verified developer app users can manage their developer app API tokens from the website UI.

#### Developer APP

The Developer app enables access to the eToroX FIX Server.  
Requests for developer apps are currently individually reviewed by Customer Support, and manually approved.

#### API tokens

API tokens are generated based on the developer app. Users can manage multiple API tokens, by identifying each API token with a customized token name. When tokens are generated, the following information becomes accessible:

* API key - A UUID id of this token.
* Private Key - A private key used for signing.

> Important! This information cannot be retrieved and must be stored by the user

#### Benefits of Authentication Process

* Access to API granted by creating specific API tokens.
* API token can only be created with 2FA verification.
* API token data only available during creation.
* API token credentials are comprised of an asymmetric RSA private/public pair of keys.
* During API token creation, the user receives a private key, to sign data.
* eToroX stores only the asymmetric public key. Even if a user has Read Access to our servers, this information is completely inaccessible, and user identities cannot be forged.
* As part of the authentication process, eToroX mitigates "man-in-the-middle" attacks using a combination of TLS encryption, a timestamp and nonce (Number Only Used Once) validations with asymmetric signature signing. Note: Sensitive information should NEVER be passed in API communication.
* As part of the authentication process, eToroX mitigates replay attacks by verifying both nonce and timestamp verifications.
* API tokens permissions can be scoped when created. Scoping ensures that a token is only used for specific actions.
* API token details are immutable. They can always be deleted, but never modified or retrieved.

#### Authentication process

The authentication process requires the following parameters:

* API key - A UUID identifies this token
* Private key - An asymmetric RSA private key that should never be shared
* Timestamp - Number of milliseconds since epoch of the moment the signature was created (example: 1567334955567)
* Nonce - A UUID

eToroX FIX Server verifies user identity in Logon (A) message based on the following:

* Field SenderCompID (49) - must be API key
* Field Password (554) - must be in format {signature}_{nonce}_{timestamp}

#### Create a signature

Asymmetric signatures are generated by the user to “prove” to the eToroX server that the user has access to their own private keys. eToroX then confirms this by running a similar but asymmetric process with the public key equivalence. The following parameters are used for signature generation:
 
* API key
* Private key
* Nonce
* Milliseconds since epoch

#### Private key

The private keys generated by eToroX are:

* PKCS8 encoded.
* PEM formatted.
* Cipher AES-256-CBC
* Empty string as the passphrase ``.

Some libraries may require the user to convert the private key into different encoding. This can be done by using the openssl command line tool.

#### Signing process

1. The payload the user signs is a concatenation of the nonce and milliseconds since epoch.  
2. The payload should be signed with a SHA256 signature algorithm and the result should be base64 encoded.  
3. The password is a concatenation of the signature, nonce and timestamp in format {signature}_{nonce}_{timestamp}  

```csharp
var privateKeyBytes = Convert.FromBase64String(privateKey);
using (var signer = RSA.Create())
{
   signer.ImportEncryptedPkcs8PrivateKey(new byte[] { }, privateKeyBytes, out _);
   var nonce = Guid.NewGuid().ToString();
   var timestamp = DateTimeOffset.Now.ToUnixTimeMilliseconds();
   var payload = $"{nonce}{timestamp}";
   var signatureHash = signer.SignData(Encoding.UTF8.GetBytes(payload), HashAlgorithmName.SHA256, RSASignaturePadding.Pkcs1);
   var signature = Convert.ToBase64String(signatureHash);
   var password = $"{signature}_{nonce}_{timestamp}";
   //Construct Logon message
   //SenderCompID = API key
   //Password = password
}
```
