# eToroX http API

Most of eToroX API functionality requires user authentication, described in full in the section [eToroX authentication](./authentication).

## General info
* The base endpoint is provided by the website when creating a developer app.
* All endpoints return either a JSON object or array.
* HTTP `4XX` return codes are used for malformed requests;
  the issue is on the sender's side.
* The HTTP `429` return code is used when breaking a request rate limit.
* HTTP `5XX` return codes are used for internal server errors, i.e. the issue is on
  eToroX's side.
  It is important to **NOT** treat this as a failed operation; the execution status is
  **UNKNOWN** and could have been a success.
* Any endpoint can return an ERROR; the error payload is as follows:
    * errorCode - a programmatic identifier for the error.
    * message - human readable description of the error.
    * refId - Reference unique id.
```json
{
    "errorCode" : "serverError",
    "refId" : "4fed797a-c31c-4474-a705-ac856d70790f",
    "message":"Server error"
}
```

## Rate limits
eToroX rate limits every *developer app* to 100 requests per minute. There is also a burst limit of 10 requests per second.
The 429 status code is returned if either limit is violated.

## API parameters 
All private API requests require the following information:
* `correlationId` - A random UUID (a unique identifier for each request, used mainly for support purposes).
* `user-agent` - Name of the application performing the API request.
* `ex-access-key` - The token API key, a UUID.
* `ex-access-sign`- The generated signature. See [eToroX authentication](authentication).
* `ex-access-timestamp` - The UNIX  timestamp used to generate the signature. The timestamp should be as close to the current timestamp as possible, or servers will fail due to excessive time differences.
* `ex-access-nonce` - A UUID that should not repeat. We use this IP to validate that a request does not repeat within a specific timeframe.

```embed-swagger
{         
    "url": "./trading-http-api.yaml"
}
```