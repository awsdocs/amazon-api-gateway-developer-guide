# Invoking a WebSocket API<a name="apigateway-how-to-call-websocket-api"></a>

After you've deployed your WebSocket API, client applications can connect to it and send messages to it—and your backend service can send messages to connected client applications:
+ You can use `wscat` to connect to your WebSocket API and send messages to it to simulate client behavior\. See [Use `wscat` to connect to a WebSocket API and send messages to it](apigateway-how-to-call-websocket-api-wscat.md)\.
+ You can use the @connections API from your backend service to send a callback message to a connected client, get connection information, or disconnect the client\. See [Use `@connections` commands in your backend service](apigateway-how-to-call-websocket-api-connections.md)\.
+ A client application can use its own WebSocket library to invoke your WebSocket API\.