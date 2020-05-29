# Working with HTTP APIs<a name="http-api"></a>

HTTP APIs enable you to create RESTful APIs with lower latency and lower cost than REST APIs\.

You can use HTTP APIs to send requests to AWS Lambda functions or to any routable HTTP endpoint\.

For example, you can create an HTTP API that integrates with a Lambda function on the backend\. When a client calls your API, API Gateway sends the request to the Lambda function and returns the function's response to the client\.

HTTP APIs support [OpenID Connect](https://openid.net/connect/) and [OAuth 2\.0](https://oauth.net/2/) authorization\. They come with built\-in support for cross\-origin resource sharing \(CORS\) and automatic deployments\.

You can create HTTP APIs by using the AWS Management Console, the AWS CLI, APIs, AWS CloudFormation, or SDKs\. HTTP APIs use API Gateway [Version 2 APIs](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/api-reference.html), which previously supported only WebSocket APIs\.

**Topics**
+ [Developing an HTTP API in API Gateway](http-api-develop.md)
+ [Publishing HTTP APIs for customers to invoke](http-api-publish.md)
+ [Protecting your HTTP API](http-api-protect.md)
+ [Monitoring your HTTP API](http-api-monitor.md)
+ [Troubleshooting issues with HTTP APIs](http-api-troubleshooting.md)