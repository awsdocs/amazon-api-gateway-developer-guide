# HTTP APIs<a name="http-api"></a>


|  | 
| --- |
| HTTP APIs are in beta for Amazon API Gateway and are subject to change\. | 

HTTP APIs enable you to create RESTful APIs with lower latency and lower cost than REST APIs\.

You can use HTTP APIs to send requests to AWS Lambda functions or to any routable HTTP endpoint\.

For example, you can create an HTTP API that integrates with a Lambda function on the backend\. When a client calls your API, API Gateway sends the request to the Lambda function and returns the function's response to the client\.

HTTP APIs support [OpenID Connect](https://openid.net/connect/) and [OAuth 2\.0](https://oauth.net/2/) authorization\. They come with built\-in support for CORS and automatic deployments\.

You can create HTTP APIs by using the AWS Management Console, the AWS CLI, APIs, AWS CloudFormation, or SDKs\. HTTP APIs use API Gateway [Version 2 APIs](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/api-reference.html), which previously supported only WebSocket APIs\.

**Topics**
+ [HTTP API Concepts](http-api-concepts.md)
+ [Choosing Between HTTP APIs and REST APIs](http-api-vs-rest.md)
+ [Creating an HTTP API](http-api-examples.md)
+ [Migrating to an HTTP API](http-api-import.md)
+ [Configuring Logging for an HTTP API](http-api-logging.md)
+ [HTTP API Quotas](http-api-quotas.md)