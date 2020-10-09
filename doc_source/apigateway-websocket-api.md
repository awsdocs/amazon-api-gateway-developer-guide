# Working with WebSocket APIs<a name="apigateway-websocket-api"></a>

A WebSocket API in API Gateway is a collection of WebSocket routes that are integrated with backend HTTP endpoints, Lambda functions, or other AWS services\. You can use API Gateway features to help you with all aspects of the API lifecycle, from creation through monitoring your production APIs\.

API Gateway WebSocket APIs are bidirectional\. A client can send messages to a service, and services can independently send messages to clients\. This bidirectional behavior enables richer client/service interactions because services can push data to clients without requiring clients to make an explicit request\. WebSocket APIs are often used in real\-time applications such as chat applications, collaboration platforms, multiplayer games, and financial trading platforms\.

For an example app to get started with, see [simple\-websockets\-chat\-app](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:729047367331:applications~simple-websockets-chat-app)\.

In this section, you can learn how to develop, publish, protect, and monitor your WebSocket APIs using API Gateway\.

**Topics**
+ [About WebSocket APIs in API Gateway](apigateway-websocket-api-overview.md)
+ [Developing a WebSocket API in API Gateway](websocket-api-develop.md)
+ [Publishing WebSocket APIs for customers to invoke](websocket-api-publish.md)
+ [Protecting your WebSocket API](websocket-api-protect.md)
+ [Monitoring WebSocket APIs](websocket-api-monitor.md)