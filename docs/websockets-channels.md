# eToroX WebSockets channels

eToroX WebSocket streaming servers offer real-time market data updates and user specific data. WebSockets is a bi-directional protocol offering fastest real-time data, helping users to build real-time applications.

> Note: eToroX only provides streaming services via WebSockets to identified users.

## WebSockets integration documentation

Documentation on WebSocket integration available on the following pages:
* [Websockets](websockets-api.md "Websockets")
* [Websockets SignalR](websockets-signal-r.md "Websockets SignalR")

## Instrument channels

Instruments also known as markets or currency pairs: btcusdex, btceth and more...

The full list of instruments should be retrieved:
* Any currency pair available from the exchange UI.
* Any instrument from the HTTPS API resource `/api/v1/instruments`
    * More information can be found in the section [eToroX HTTPS API](https-api)

Each instrument channel support the event `marketData`.
The event streams both public order & public trades related to the requested channels.

Example: Subscription request (Without signalR request format):
```json
{
    "channels":["btcusdex"],
    "event": "marketData"
}
```
