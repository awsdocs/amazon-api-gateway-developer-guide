# HTTP API Logging Variables<a name="http-api-logging-variables"></a>


|  | 
| --- |
| HTTP APIs are in beta for Amazon API Gateway and are subject to change\. | 

## <a name="http-api-logging-variables.table"></a>

You can use the following variables to customize HTTP API access logs\. To learn more about access logs for HTTP APIs, see [Configuring Logging for an HTTP API](http-api-logging.md)\.


| Parameter | Description | 
| --- | --- | 
| $context\.accountId |  The API owner's AWS account ID\.  | 
| $context\.apiId |  The identifier API Gateway assigns to your API\.  | 
| $context\.authorizer\.claims\.property |  A property of the claims returned from the JSON Web Token \(JWT\) after the method caller is successfully authenticated\. For more information, see [JWT Authorizers](http-api-jwt-authorizer.md)\.  Calling `$context.authorizer.claims` returns null\.   | 
| $context\.awsEndpointRequestId |  The AWS endpoint's request ID\.  | 
| $context\.dataProcessed | The amount of data processed in bytes\. | 
| $context\.domainName |  The full domain name used to invoke the API\. This should be the same as the incoming `Host` header\.  | 
| $context\.domainPrefix |  The first label of the `$context.domainName`\. This is often used as a caller/customer identifier\.  | 
| $context\.error\.message |  A string containing an API Gateway error message\.  | 
| $context\.error\.messageString | The quoted value of $context\.error\.message, namely "$context\.error\.message"\. | 
| $context\.error\.responseType |  A type of `GatewayResponse`\. For more information, see [Monitor WebSocket API Execution with CloudWatch](apigateway-websocket-api-logging.md) and [Set up Gateway Responses to Customize Error Responses](customize-gateway-responses.md)\.  | 
| $context\.extendedRequestId | The extended ID that API Gateway assigns to the API request, which contains more useful information for debugging/troubleshooting\. | 
| $context\.httpMethod |  The HTTP method used\. Valid values include: `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, and `PUT`\.  | 
| $context\.identity\.sourceIp |  The source IP address of the TCP connection making the request to API Gateway\.  You should not trust this value if there is any chance that the `X-Forwarded-For` header could be forged\.   | 
| $context\.identity\.userAgent |  The [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) header of the API caller\.  | 
| $context\.path | The request path\. For example, /\{stage\}/root/child\.  | 
| $context\.protocol | The request protocol, for example, HTTP/1\.1\. | 
| $context\.requestId |  The ID that API Gateway assigns to the API request\.  | 
| $context\.requestTime | The [CLF](https://httpd.apache.org/docs/1.3/logs.html#common)\-formatted request time \(dd/MMM/yyyy:HH:mm:ss \+\-hhmm\)\. | 
| $context\.requestTimeEpoch | The [Epoch](https://en.wikipedia.org/wiki/Unix_time)\-formatted request time\. | 
| $context\.routeKey |  The route key of the API request, for example `/pets`\.  | 
| $context\.stage |  The deployment stage of the API request \(for example, `Beta` or `Prod`\)\.  | 
| $context\.integrationLatency | The integration latency in ms\. | 
| $context\.integrationStatus | For Lambda proxy integration, this parameter represents the status code returned from AWS Lambda, not from the backend Lambda function\. | 
| $context\.responseLatency | The response latency in ms\. | 
| $context\.responseLength | The response payload length\. | 
| $context\.status | The method response status\. | 