# API Gateway use cases<a name="api-gateway-overview-developer-experience"></a>

**Topics**
+ [Use API Gateway to create HTTP APIs](#api-gateway-overview-http)
+ [Use API Gateway to create REST APIs](#api-gateway-overview-rest)
+ [Use API Gateway to create WebSocket APIs](#api-gateway-overview-websocket)
+ [Who uses API Gateway?](#apigateway-who-uses-api-gateway)

## Use API Gateway to create HTTP APIs<a name="api-gateway-overview-http"></a>

HTTP APIs enable you to create RESTful APIs with lower latency and lower cost than REST APIs\.

You can use HTTP APIs to send requests to AWS Lambda functions or to any publicly routable HTTP endpoint\.

For example, you can create an HTTP API that integrates with a Lambda function on the backend\. When a client calls your API, API Gateway sends the request to the Lambda function and returns the function's response to the client\.

HTTP APIs support [OpenID Connect](https://openid.net/connect/) and [OAuth 2\.0](https://oauth.net/2/) authorization\. They come with built\-in support for cross\-origin resource sharing \(CORS\) and automatic deployments\.

To learn more, see [Choosing between HTTP APIs and REST APIs](http-api-vs-rest.md)\.

## Use API Gateway to create REST APIs<a name="api-gateway-overview-rest"></a>

An API Gateway REST API is made up of resources and methods\. A resource is a logical entity that an app can access through a resource path\. A method corresponds to a REST API request that is submitted by the user of your API and the response returned to the user\. 

For example, `/incomes` could be the path of a resource representing the income of the app user\. A resource can have one or more operations that are defined by appropriate HTTP verbs such as GET, POST, PUT, PATCH, and DELETE\. A combination of a resource path and an operation identifies a method of the API\. For example, a `POST /incomes` method could add an income earned by the caller, and a `GET /expenses` method could query the reported expenses incurred by the caller\. 

The app doesn't need to know where the requested data is stored and fetched from on the backend\. In API Gateway REST APIs, the frontend is encapsulated by *method requests* and *method responses*\. The API interfaces with the backend by means of *integration requests* and *integration responses*\.

For example, with DynamoDB as the backend, the API developer sets up the integration request to forward the incoming method request to the chosen backend\. The setup includes specifications of an appropriate DynamoDB action, required IAM role and policies, and required input data transformation\. The backend returns the result to API Gateway as an integration response\. 

To route the integration response to an appropriate method response \(of a given HTTP status code\) to the client, you can configure the integration response to map required response parameters from integration to method\. You then translate the output data format of the backend to that of the frontend, if necessary\. API Gateway enables you to define a schema or model for the [payload ](https://en.wikipedia.org/wiki/Payload_(computing)) to facilitate setting up the body mapping template\.

API Gateway provides REST API management functionality such as the following:
+ Support for generating SDKs and creating API documentation using API Gateway extensions to OpenAPI
+ Throttling of HTTP requests

## Use API Gateway to create WebSocket APIs<a name="api-gateway-overview-websocket"></a>

In a WebSocket API, the client and the server can both send messages to each other at any time\. Backend servers can easily push data to connected users and devices, avoiding the need to implement complex polling mechanisms\.

For example, you could build a serverless application using an API Gateway WebSocket API and AWS Lambda to send and receive messages to and from individual users or groups of users in a chat room\. Or you could invoke backend services such as AWS Lambda, Amazon Kinesis, or an HTTP endpoint based on message content\.

You can use API Gateway WebSocket APIs to build secure, real\-time communication applications without having to provision or manage any servers to manage connections or large\-scale data exchanges\. Targeted use cases include real\-time applications such as the following:
+ Chat applications
+ Real\-time dashboards such as stock tickers
+ Real\-time alerts and notifications

API Gateway provides WebSocket API management functionality such as the following:
+ Monitoring and throttling of connections and messages
+ Using AWS X\-Ray to trace messages as they travel through the APIs to backend services
+ Easy integration with HTTP/HTTPS endpoints

## Who uses API Gateway?<a name="apigateway-who-uses-api-gateway"></a>

There are two kinds of developers who use API Gateway: API developers and app developers\.

An API developer creates and deploys an API to enable the required functionality in API Gateway\. The API developer must be an IAM user in the AWS account that owns the API\.

An app developer builds a functioning application to call AWS services by invoking a WebSocket or REST API created by an API developer in API Gateway\.

The app developer is the customer of the API developer\. The app developer doesn't need to have an AWS account, provided that the API either doesn't require IAM permissions or supports authorization of users through third\-party federated identity providers supported by [Amazon Cognito user pool identity federation](https://docs.aws.amazon.com/cognito/latest/developerguide/)\. Such identity providers include Amazon, Amazon Cognito user pools, Facebook, and Google\.

### Creating and managing an API Gateway API<a name="api-gateway-overview-developer-experience-managing-api"></a>

An API developer works with the API Gateway service component for API management, named `apigateway`, to create, configure, and deploy an API\.

As an API developer, you can create and manage an API by using the API Gateway console, described in [Getting started with Amazon API Gateway](getting-started.md), or by calling the [API references](api-ref.md)\. There are several ways to call this API\. They include using the AWS Command Line Interface \(AWS CLI\), or by using an AWS SDK\. In addition, you can enable API creation with [AWS CloudFormation templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html) or \(in the case of REST APIs and HTTP APIs\) [Working with API Gateway extensions to OpenAPI](api-gateway-swagger-extensions.md)\. 

For a list of Regions where API Gateway is available, as well as the associated control service endpoints, see [Amazon API Gateway Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/apigateway.html)\.

### Calling an API Gateway API<a name="api-gateway-overview-developer-experience-invoking-api"></a>

An app developer works with the API Gateway service component for API execution, named `execute-api`, to invoke an API that was created or deployed in API Gateway\. The underlying programming entities are exposed by the created API\. There are several ways to call such an API\. To learn more, see [Invoking a REST API in Amazon API Gateway](how-to-call-api.md) and [Invoking a WebSocket API](apigateway-how-to-call-websocket-api.md)\.