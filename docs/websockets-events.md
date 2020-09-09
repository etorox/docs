
# eToroX WebSockets Events

  

Market events are triggered on every market change, this keeps the user aware of the current state of a market.
There are two types of channels, public and private.

## Public events
The public channel holds any changes that affects the market. Events published to the public channel are relevant for all users that are trading a certain market.

## Private events
Sensitive information is provided on private channel only. Private messages has slightly different format which includes additional details that are only available to the user subscribed on his own orders and trades.
The private events holds information of the user's positions and trades.
  
## Events types
Events are triggered on every market change, such as a newly opened position or a trade. 
Here is a list of the possible events that are published to webSocket - 

* order created
* order cancelled 
* order updated
* order completed
* trade completed

Those events are relevant for private and public channels, which means any market change will trigger 2 events to be published, one to the private channel (of the user performing the action) and another one to the public channel, with slightly different format.

The following section will describe the events triggering a message and the expected data which the user subscribed will receive on public channel and on private channel separately.

### Order Created
Order created event will be triggered when a new position is published. A message to private channel will be sent only to the user opening the position.

#### Private message 
```json 
{
	"market":"btcusd",
	"side":"bid",
	"id":"466e190f-55eb-457e-b4b7-8393f7f01a7b",
	"name":"order_created",
	"volume":1,
	"strategy":"limit",
	"price":1.4,
	"state":"wait",
	"updated_at":"2020-08-03T10:16:17.631Z",
	"tif":"gtc"
}
```
#### Public message 
```json
 {
	"market":"btcusd",
	"side":"bid",
	"name":"order_created",
	"volume":1,
	"price":1.4,
	"state":"wait",
	"updated_at":"2020-08-03T10:16:17.631Z",
	"id":"466e190f-55eb-457e-b4b7-8393f7f01a7b"
}
```


### Order Updated
Order updated event will be triggered when a position was updated, such as in cases of partial fill of an order, when the original order has changed.

#### Private message 
```json 
{
	"market":"btcusd",
	"side":"ask",
	"name":"order_updated",
	"strategy":"limit",
	"volume":89.6435,
	"price":2.1,
	"delta":"-1.5",
	"state":"open",
	"updated_at":"2020-08-03T10:23:55.879Z",
	"tif":"gtd",
	"expiration_date":"2020-09-03T10:23:55.879Z",
	"id":"be84045a-2471-412f-b205-b38cf714fbd4"
}
```
#### Public message 
```json
{
	"market":"btcusd",
	"side":"ask",
	"name":"order_updated",
	"volume":89.6435,
	"price":2.1,
	"delta":"-1.5",
	"state":"open",
	"updated_at":"2020-08-03T10:23:55.879Z",
	"id":"be84045a-2471-412f-b205-b38cf714fbd4"
}
```


### Order Cancelled
Order cancelled event will be triggered when a position was cancelled by the position holder or by the system. A message to private channel will be sent only to the user holding the position.

#### Private message 
```json 
{
	"price":1.4,
	"strategy":"limit",
	"delta":-1,
	"volume":1,
	"market":"btcusd",
	"id":"466e190f-55eb-457e-b4b7-8393f7f01a7b",
	"side":"bid",
	"name":"order_canceled",
	"state":"cancelled",
	"updated_at":"2020-08-03T10:17:36.592Z",
	"tif":"gtc"
}
```
#### Public message 
```json
{
	"origin_volume":1,
	"price":1.4,
	"id":"466e190f-55eb-457e-b4b7-8393f7f01a7b",
	"volume":1,
	"delta":-1,
	"market":"btcusd",
	"name":"order_canceled",
	"side":"bid",
	"state":"cancelled",
	"update_at":"2020-08-03T10:17:36.592Z"
}
```

### Order Completed
Order completed event will be triggered when a position was closed due to a trade. A message to the private channel will be sent to the holders of the orders involved in the trade.

#### Private message 
```json 
{
	"id":"94b75864-8cbe-44e1-a638-98fc5b9e2804",
	"market":"btcusd",
	"side":"ask",
	"name":"order_completed",
	"volume":1,
	"strategy":"limit",
	"price":0.5,
	"state":"completed",
	"updated_at":"2020-08-03T10:18:37.578Z"
}
```
#### Public message 
```json
{
	"id":"94b75864-8cbe-44e1-a638-98fc5b9e2804",
	"market":"btcusd",
	"side":"ask",
	"name":"order_completed",
	"volume":1,
	"delta":-1,
	"price":0.5,
	"state":"completed",
	"updated_at":"2020-08-03T10:18:37.578Z"
}
```

### Trade Completed
Trade completed event will be triggered when a trade was performed. A message to the private channel will be sent to the holders of the orders involved in the trade.

#### Private message 
```json 
{
	"market":"btcusd",
	"id":"b1e4f403-d572-11ea-afd6-42010adc003c",
	"price":2,
	"volume":1,
	"side":"ask",
	"name":"trade_completed",
	"state":"completed",
	"created_at":"2020-08-03T10:18:37.578Z",
	"order_id":"94b75864-8cbe-44e1-a638-98fc5b9e2804",
	"execution_type":"taker"
}
```
#### Public message 
```json
{
	"tid":"b1e4f403-d572-11ea-afd6-42010adc003c",
	"price":2,
	"amount":"1.000000000000000000",
	"created_at":"2020-08-03T10:18:37.578Z",
	"name":"trade_completed",
	"market":"btcusd"
}
```

