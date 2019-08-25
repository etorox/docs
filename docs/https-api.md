# eToroX http API

Most of eToroX API functionality requires user authentication.
Authentication process is fully described by [eToroX authentication](./authentication).

## General info
* The base endpoint is provided by the UI website when creating a developer app.
* All endpoints return either a JSON object or array.
* HTTP `4XX` return codes are used for malformed requests;
  the issue is on the sender's side.
* HTTP `429` return code is used when breaking a request rate limit.
* HTTP `5XX` return codes are used for internal server errors; the issue is on
  eToroX's side.
  It is important to **NOT** treat this as a failure operation; the execution status is
  **UNKNOWN** and could have been a success.
* Any endpoint can return an ERROR; the error payload is as follows:
    * errorCode - a programtic identifier for the error.
    * message - human readable description of the error.
    * incidentId - Incident unique id.
```json
{
    "errorCode" : "serverError",
    "incidentId" : "4fed797a-c31c-4474-a705-ac856d70790f",
    "message":"Server error"
}
```

## Rate limits
eToroX rate limit every *developer app* with 100 requests per minute.
In addition, there is a burst limit of 10 request per second.

429 status code will be returned when either limit will be vaiolated.

## Private API 
All private API requests requires the following information:
* `correlationId` - A random UUID, it's a unique identifier for each request, mainly used for support purposes.
* `user-agent` - Some name for the application preforming this API request.
* `ex-access-key` - The token API key, a UUID.
* `ex-access-sign`- The signature generated as described in [eToroX authentication](authentication).
* `ex-access-timestamp` - The unix timestamp that was used to generate the signaure, also timestamp should be as much close to current timestamp as servers will fail big time differences.
* `ex-access-nonce` - UUID that shouldn't repeat, we use this IP to validate that a request will not be repeating in a specifc timeframe.

```embed-swagger
{         
    "url": "./trading-http-api.yaml"
}
```