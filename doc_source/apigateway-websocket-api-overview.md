# About WebSocket APIs in API Gateway<a name="apigateway-websocket-api-overview"></a>

In API Gateway you can create a WebSocket API as a stateful frontend for an AWS service \(such as Lambda or DynamoDB\) or for an HTTP endpoint\. The WebSocket API invokes your backend based on the content of the messages it receives from client apps\.

Unlike a REST API, which receives and responds to requests, a WebSocket API supports two\-way communication between client apps and your backend\. The backend can send callback messages to connected clients\.

In your WebSocket API, incoming JSON messages are directed to backend integrations based on routes that you configure\. \(Non\-JSON messages are directed to a `$default` route that you configure\.\)

A *route* includes a *route key*, which is the value that is expected once a *route selection expression* is evaluated\. The `routeSelectionExpression` is an attribute defined at the API level\. It specifies a JSON property that is expected to be present in the message payload\. For more information about route selection expressions, see [Route selection expressions](websocket-api-develop-routes.md#apigateway-websocket-api-route-selection-expressions)\.

For example, if your JSON messages contain an `action` property, and you want to perform different actions based on this property, your route selection expression might be `${request.body.action}`\. Your routing table would specify which action to perform by matching the value of the `action` property against the custom route key values that you have defined in the table\.

There are three predefined routes that can be used: `$connect`, `$disconnect`, and `$default`\. In addition, you can create custom routes\.
+ API Gateway calls the `$connect` route when a persistent connection between the client and a WebSocket API is being initiated\.
+ API Gateway calls the `$disconnect` route when the client or the server disconnects from the API\.
+ API Gateway calls a custom route after the route selection expression is evaluated against the message if a matching route is found; the match determines which integration is invoked\.
+ API Gateway calls the `$default` route if the route selection expression cannot be evaluated against the message or if no matching route is found\.

For more information about the `$connect` and `$disconnect` routes, see [Managing connected users and client apps: `$connect` and `$disconnect` routes](apigateway-websocket-api-route-keys-connect-disconnect.md)\.

For more information about the `$default` route and custom routes, see [Invoking your backend integration: `$default` Route and custom routes](apigateway-websocket-api-routes-integrations.md)\.

Backend services can send data to connected client apps\. For more information, see [Sending data from backend services to connected clients](apigateway-websocket-api-data-from-backend.md)\.