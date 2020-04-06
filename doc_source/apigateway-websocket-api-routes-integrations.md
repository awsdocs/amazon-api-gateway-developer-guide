# Invoking your backend integration: `$default` Route and custom routes<a name="apigateway-websocket-api-routes-integrations"></a>

**Topics**
+ [Using routes to process messages](#apigateway-websocket-api-overview-routes)
+ [The `$default` route](#apigateway-websocket-api-routes-about-default)
+ [Custom routes](#apigateway-websocket-api-routes-about-custom)
+ [Using API Gateway WebSocket API integrations to connect to your business logic](#apigateway-websocket-api-overview-integrations)
+ [Important differences between WebSocket APIs and REST APIs](#apigateway-websocket-api-overview-integrations-differences)

## Using routes to process messages<a name="apigateway-websocket-api-overview-routes"></a>

In API Gateway WebSocket APIs, messages can be sent from the client to your backend service and vice versa\. Unlike HTTP's request/response model, in WebSocket the backend can send messages to the client without the client taking any action\.

Messages can be JSON or non\-JSON\. However, only JSON messages can be routed to specific integrations based on message content\. Non\-JSON messages are passed through to the backend by the `$default` route\.

**Note**  
API Gateway supports message payloads up to 128 KB with a maximum frame size of 32 KB\. If a message exceeds 32 KB, you must split it into multiple frames, each 32 KB or smaller\. If a larger message \(or frame\) is received, the connection is closed with code 1009\.  
Currently binary payloads are not supported\. If a binary frame is received, the connection is closed with code 1003\. However, it is possible to convert binary payloads to text\. See [Working with binary media types for WebSocket APIs](websocket-api-develop-binary-media-types.md)\.

With WebSocket APIs in API Gateway, JSON messages can be routed to execute a specific backend service based on message content\. When a client sends a message over its WebSocket connection, this results in a *route request* to the WebSocket API\. The request will be matched to the route with the corresponding route key in API Gateway\. You can set up a route request for a WebSocket API in the API Gateway console, by using the AWS CLI, or by using an AWS SDK\.

**Note**  
In the AWS CLI and AWS SDKs, you can create routes before or after you create integrations\. Currently the console does not support reuse of integrations, so you must create the route first and then create the integration for that route\.

You can configure API Gateway to perform validation on a route request before proceeding with the integration request\. If the validation fails, API Gateway fails the request without calling your backend, sends a `"Bad request body"` gateway response similar to the following to the client, and publishes the validation results in CloudWatch Logs: 

```
{"message" : "Bad request body", "connectionId": "{connectionId}", "messageId": "{messageId}"}
```

This reduces unnecessary calls to your backend and lets you focus on the other requirements of your API\.

You can also define a route response for your API's routes to enable two\-way communication\. A route response describes what data will be sent to your client upon completion of a particular route's integration\. It is not necessary to define a response for a route if, for example, you want a client to send messages to your backend without receiving a response \(one\-way communication\)\. However, if you don't provide a route response, API Gateway won't send any information about the result of your integration to your clients\.

## The `$default` route<a name="apigateway-websocket-api-routes-about-default"></a>

Every API Gateway WebSocket API can have a `$default` route\. This is a special routing value that can be used in the following ways:
+ You can use it together with defined route keys, to specify a "fallback" route \(for example, a generic mock integration that returns a particular error message\) for incoming messages that don't match any of the defined route keys\.
+ You can use it without any defined route keys, to specify a proxy model that delegates routing to a backend component\.
+ You can use it to specify a route for non\-JSON payloads\.

## Custom routes<a name="apigateway-websocket-api-routes-about-custom"></a>

If you want to invoke a specific integration based on message content, you can do so by creating a custom route\.

A custom route uses a route key and integration that you specify\. When an incoming message contains a JSON property, and that property evaluates to a value that matches the route key value, API Gateway invokes the integration\. \(For more information, see [About WebSocket APIs in API Gateway](apigateway-websocket-api-overview.md)\.\)

For example, suppose you wanted to create a chat room application\. You might start by creating a WebSocket API whose route selection expression is `$request.body.action`\. You could then define two routes: `joinroom` and `sendmessage`\. A client app might invoke the `joinroom` route by sending a message such as the following:

```
{"action":"joinroom","roomname":"developers"}
```

And it might invoke the `sendmessage` route by sending a message such as the following:

```
{"action":"sendmessage","message":"Hello everyone"}
```

## Using API Gateway WebSocket API integrations to connect to your business logic<a name="apigateway-websocket-api-overview-integrations"></a>

After setting up a route for an API Gateway WebSocket API, you must specify the integration you'd like to use\. As with a route, which can have a route request and a route response, an integration can have an *integration request* and an *integration response*\. An *integration request* contains the information expected by your backend in order to process the request that came from your client\. An *integration response* contains the data that your backend returns to API Gateway, and that may be used to construct a message to send to the client \(if a route response is defined\)\.

For more information about setting up integrations, see [Setting up WebSocket API integrations](apigateway-websocket-api-integrations.md)\.

## Important differences between WebSocket APIs and REST APIs<a name="apigateway-websocket-api-overview-integrations-differences"></a>

Integrations for WebSocket APIs are similar to integrations for REST APIs, except for the following differences:
+ Currently, in the API Gateway console you must create a route first and then create an integration as that route's target\. However, in the API and CLI, you can create routes and integrations independently, in any order\.
+ You can use a single integration for multiple routes\. For example, if you have a set of actions that closely relate to each other, you may want all of those routes to go to a single Lambda function\. Rather than defining the details of the integration multiple times, you can specify it once and assign it to each of the related routes\.
**Note**  
Currently the console does not support reuse of integrations, so you must create the route first and then create the integration for that route\.  
In the AWS CLI and AWS SDKs, you can reuse an integration by setting the route's target to a value of `"integrations/{integration-id}"`, where `{integration-id}"` is the unique ID of the integration to be associated with the route\.
+ API Gateway provides multiple [selection expressions](apigateway-websocket-api-selection-expressions.md) you can use in your routes and integrations\. You don't need to rely on the content type to select an input template or output mapping\. As with route selection expressions, you can define a selection expression to be evaluated by API Gateway to choose the right item\. All of them will fall back to the `$default` template if a matching template is not found\.
  + In integration requests, the template selection expression supports `$request.body.<json_path_expression>` and static values\.
  + In integration responses, the template selection expression supports `$request.body.<json_path_expression>`, `$integration.response.statuscode`, and `$integration.response.header.<headerName>`\.

In the HTTP protocol, in which requests and responses are sent synchronously; communication is essentially one\-way\. In the WebSocket protocol, communication is two\-way\. Responses are asynchronous and are not necessarily received by the client in the same order as the client's messages were sent\. In addition, the backend can send messages to the client\.

**Note**  
For a route that is configured to use `AWS_PROXY` or `LAMBDA_PROXY` integration, communication is one\-way, and API Gateway will not pass the backend response through to the route response automatically\. For example, in the case of `LAMBDA_PROXY` integration, the body that the Lambda function returns will not be returned to the client\. If you want the client to receive integration responses, you must define a route response to make two\-way communication possible\.