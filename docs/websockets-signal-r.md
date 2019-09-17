# Websocket client SignalR

SignalR client is the fastest way to integrate with eToroX streaming services.

Before starting the integration, you must create a token from the website UI, and use both `apiKey` and `privateKey`;
<!-- tabs:start -->

#### ** Node.JS **

```embed-gist
{
    "url":"https://gist.github.com/etorox/b60404cf1682420b93b9be7385221288.js",
    "elid":"1",
    "file":"step1-prepare.js"
}
```

#### ** C# **

```embed-gist
{
    "url":"https://gist.github.com/etorox/d2abbcff59395b4d52fdad5fe0dfde62.js",
    "elid":"1",
    "file":"step1-prepare.cs"
}
```

#### ** Pyhton **

soon!

<!-- tabs:end -->

## Step 2 - Connect to server
<!-- tabs:start -->

#### ** Node.JS **

```embed-gist
{
    "url":"https://gist.github.com/etorox/b60404cf1682420b93b9be7385221288.js",
    "elid":"2",
    "file":"step2-connect.js"
}
```

#### ** C# **

```embed-gist
{
    "url":"https://gist.github.com/etorox/d2abbcff59395b4d52fdad5fe0dfde62.js",
    "elid":"2",
    "file":"step2-connect.cs"
}
```

#### ** Pyhton **

soon!

<!-- tabs:end -->
## Step 3 - Authentication

At this stage, a WebSocket connection has already been created. The user must identify with the `setup` action.

* See the authentication process described in the section [eToroX authentication](authentication).
* See WebSocket data, actions and streams, described in the [Websockets API](websockets-api).

<!-- tabs:start -->

#### ** Node.JS **

```embed-gist
{
    "url":"https://gist.github.com/etorox/b60404cf1682420b93b9be7385221288.js",
    "elid":"3",
    "file":"step3-auth-signature.js"
}
```

#### ** C# **

```embed-gist
{
    "url":"https://gist.github.com/etorox/d2abbcff59395b4d52fdad5fe0dfde62.js",
    "elid":"3",
    "file":"step3-auth-signature.cs"
}
```

#### ** Pyhton **

soon!

<!-- tabs:end -->

Once you have the authentication parameters, you can now easily set up the connections


With the authentication parameters it's now very simple to setup the connections:
<!-- tabs:start -->

#### ** Node.JS **

```embed-gist
{
    "url":"https://gist.github.com/etorox/b60404cf1682420b93b9be7385221288.js",
    "elid":"4",
    "file":"step4-setup.js"
}
```

#### ** C# **

```embed-gist
{
    "url":"https://gist.github.com/etorox/d2abbcff59395b4d52fdad5fe0dfde62.js",
    "elid":"4",
    "file":"step4-setup.cs"
}
```

#### ** Pyhton **

soon!

<!-- tabs:end -->

## Step 4 - Subscribe

At this stage, a WebSocket connection has already been created and connection had been `setup`. The user must `subscripe` and stream the desired events.

<!-- tabs:start -->

#### ** Node.JS **

```embed-gist
{
    "url":"https://gist.github.com/etorox/b60404cf1682420b93b9be7385221288.js",
    "elid":"5",
    "file":"step6-subscribe.js"
}
```

#### ** C# **

```embed-gist
{
    "url":"https://gist.github.com/etorox/d2abbcff59395b4d52fdad5fe0dfde62.js",
    "elid":"5",
    "file":"step5-subscribe.cs"
}
```

#### ** Pyhton **

soon!

<!-- tabs:end -->