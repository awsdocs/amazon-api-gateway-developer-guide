# Working with HTTP APIs<a name="http-api"></a>

REST APIs and HTTP APIs are both RESTful API products\. REST APIs support more features than HTTP APIs, while HTTP APIs are designed with minimal features so that they can be offered at a lower price\. For more information, see [Choosing between REST APIs and HTTP APIs](http-api-vs-rest.md)\.

You can use HTTP APIs to send requests to AWS Lambda functions or to any routable HTTP endpoint\. For example, you can create an HTTP API that integrates with a Lambda function on the backend\. When a client calls your API, API Gateway sends the request to the Lambda function and returns the function's response to the client\.

HTTP APIs support [OpenID Connect](https://openid.net/connect/) and [OAuth 2\.0](https://oauth.net/2/) authorization\. They come with built\-in support for cross\-origin resource sharing \(CORS\) and automatic deployments\.

You can create HTTP APIs by using the AWS Management Console, the AWS CLI, APIs, AWS CloudFormation, or SDKs\.

**Topics**
+ [Developing an HTTP API in API Gateway](http-api-develop.md)
+ [Publishing HTTP APIs for customers to invoke](http-api-publish.md)
+ [Protecting your HTTP API](http-api-protect.md)
+ [Monitoring your HTTP API](http-api-monitor.md)
+ [Troubleshooting issues with HTTP APIs](http-api-troubleshooting.md)