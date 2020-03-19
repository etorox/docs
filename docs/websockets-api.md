# eToroX WebSockets API

eToroX WebSocket streaming servers offer real-time market data updates and user specific data. WebSockets is a bi-directional protocol offering fastest real-time data, helping users to build real-time applications.

> Note: eToroX provides streaming services via WebSockets only to identified users.

## General

* The base endpoint is provided by the UI website when creating a developer app.
* All WebSocket messages are JSON formatted messages separated by a special space-like character `` (`0x1e`) .
* eToroX will close WebSockets that are opened for more than 24 hours. It is highly recommended to perform a gradual recycle of new WebSockets connections prior to hard disconnection by the eToroX server.

## Ping

Both eToroX and the client want to determine whether the connection is alive.
> This `ping` is an application layer ping, which is in addition to the Websocket protocol ping initiated by the server.

User must send a `ping` frame every ~15 seconds.

Client connections are terminated if no valid `ping` is received for more than 30 seconds. If the eToroX server does not respond with a valid `ping` result, the  client should assume that the eToroX server is not accessible.

> When using [SignalR](signal-r) it's transparently managed by the SignalR client.

## Rate limit

eToroX will update the limits of creating WebSockets.

## Server side connection hangups

The eToroX server will terminate client connections in any of the following scenarios:

* The client didn't perform the `setup` action properly.
* The client didn't perform the http handshake in time.
* After completing the http handshake, the client didn't immediately complete `setup`.
* The client did not ping the server within a reasonable timeframe.

## WebSocket protocol

Any WebSocket compliant (`rfc6455`) client should be able to connect to eToroX streaming services.

Currently users can choose one of the following clients for integration:
* [SignalR](signal-r) (Cross platform ASP.NET Core SignalR) - The easiest approach, where SignalR handles most parts of the transport protocol for the user, leaving them only to handle actions required by eToroX.
* Websocket - The user needs  to handle all transport protocol actions in addition to actions required by eToroX.

*eToroX currently only provides documentation for integration with the SignalR client. Documentation regarding integration details with plain Websocket will be provided at a future date*

## Terminology
Phrase | Meaning
--- | --- 
WebSocket protocol | The prefefined procedural methods of creating and maintaining a Websocket connection to eToroX servers, including frames and json formats defined by SignalR.
Channel | A logical subject / source of information.
Event | A channel may stream multiple occurrences that have happened on that specific channel.
Topic | A combination of a single channel and a single event.
Result | A single result or multiple results from the same invocation.
Action | A method or a frame that should be sent by the user in order to achieve a result.
Stream | An action that results in real time, multiple, on-going results.
Subscription | A request for a stream of information, where the stream is a technical term for the information passed. The subscription is the logical term that defines the streams.

## eToroX actions / streams

### Action - `setup`
eToroX streaming services are only permitted for identified users. The setup action is performed to "prove" to eToroX that the user of the WebSocket is an identified user, and that it has access to their own private key.

> Do not share your private keys! eToroX will never ask you to share or send your private keys.

For more details about the authentication process, see the section [eToroX authentication](authentication).

The `setup` action receives the following mandatory parameters as a single json formatted key/value object:

Name | Value
--- | ---
`apiKey`:string | The token api key.
`nonce`:string | A unique UUID that was used when generating the signature.
`timestamp`:int | Milliseconds since epoch used when generating the signature.
`signature`:string | The result of the signature process (described in the section[eToroX authentication](authentication) )

### Stream - `subscribe`

A single websocket connection can maintain multiple streams of different channels and events.

The `subscribe` action adds an additional stream of data of the requested channels and event to the current websocket session

> Clients may invoke the `subscribe` action as many times as they wish as long as the action passes the required validations.

The `subscribe` action receives the following mandatory fields as a json formatted key/value object:

Name | Value
--- | ---
`channels`:string[] | The list of channels to be added to the requested stream.
`event`:string | The name of the event to be selected from all channels to be added to the requested stream.

The following subscription request (Without signalR request format):
```json
{
    "channels":["btcusdex"],
    "event": "orderbook"
}
```
Produces events like the following event (Without signalR response format):
```json
{
    "name": "order_created",
    "market": "btcusdex",
    "volume": 11,
    "price": 0.23,
    //....// 
}
```

#### `subscribe` validations
* Multiple client subscriptions cannot be subscribed to the same topic (channel/event combination). Should the same topic be requested more than once, the `subscribe` request will fail.
* Each client subscription request should be from a list of channels and their events. If a topic is requested that is not listed by eToroX, or is not active, the `subscribe` request will fail.
* If the optional `fields` selector is sent than it must be ordered in alphanumerically.

#### Stream `fields` selector
In addition to the signalR formatted frames, eToroX servers returns a key/value json formatted object with all `fields` and data for each event. While being very comfortable it significantly increases bandwidth. It is highly  recommended to activate a fields selector when creating the desired stream. As a result the client will receive the list of requested values without the schema, and will be able to determine which of the values defines which property according to the requested order.

The `subscribe` action receives the following mandatory and optional fields selector fields as a json formatted key/value object:

Name | Value
--- | ---
`channels`:string[] | The list of channels to be added to the requested stream.
`event`:string | The name of the event to be selected from all channels to be added to the requested stream.
`fields`:string[] | The list of fields to select from the event data.

The following subscription request (Without signalR request format):
```json
{
    "channels":["btcusdex"],
    "event": "orderbook",
    "fields":["name" , "price" , "volume"]
}
```

Produces events like the following events (Without signalR request format):
```json
["order_created" , 1.2 , 0.5]
```
> The order of the values is arranged in the same order as the requested fields in the subscription request.
