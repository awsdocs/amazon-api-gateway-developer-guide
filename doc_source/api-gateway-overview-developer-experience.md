# API Gateway Use Cases<a name="api-gateway-overview-developer-experience"></a>

**Topics**
+ [Use API Gateway to Create REST APIs](#api-gateway-overview-rest)
+ [Use API Gateway to Create WebSocket APIs](#api-gateway-overview-websocket)
+ [Who Uses API Gateway?](#apigateway-who-uses-api-gateway)

## Use API Gateway to Create REST APIs<a name="api-gateway-overview-rest"></a>

An API Gateway REST API is made up of resources and methods\. A resource is a logical entity that an app can access through a resource path\. A method corresponds to a REST API request that is submitted by the user of your API and the response returned to the user\. 

For example, `/incomes` could be the path of a resource representing the income of the app user\. A resource can have one or more operations that are defined by appropriate HTTP verbs such as GET, POST, PUT, PATCH, and DELETE\. A combination of a resource path and an operation identifies a method of the API\. For example, a `POST /incomes` method could add an income earned by the caller, and a `GET /expenses` method could query the reported expenses incurred by the caller\. 

The app does not need to know where the requested data is stored and fetched from on the backend\. In API Gateway REST APIs, the frontend is encapsulated by *method requests* and *method responses*\. The API interfaces with the backend by means of *integration requests* and *integration responses*\.

For example, with DynamoDB as the backend, the API developer sets up the integration request to forward the incoming method request to the chosen backend\. The setup includes specifications of an appropriate DynamoDB action, required IAM role and policies, and required input data transformation\. The backend returns the result to API Gateway as an integration response\. To route the integration response to an appropriate method response \(of a given HTTP status code\) to the client, you can configure the integration response to map required response parameters from integration to method\. You then translate the output data format of the backend to that of the frontend, if necessary\. API Gateway enables you to define a schema or model for the [payload ](https://en.wikipedia.org/wiki/Payload_(computing)) to facilitate setting up the body mapping template\.

API Gateway provides REST API management functionality such as:
+ Support for generating SDKs and creating API documentation using API Gateway extensions to OpenAPI
+ Throttling of HTTP requests

## Use API Gateway to Create WebSocket APIs<a name="api-gateway-overview-websocket"></a>

In a WebSocket API, the client and the server can both send messages to each other at any time\. Backend servers can easily push data to connected users and devices, avoiding the need to implement complex polling mechanisms\.

For example, you could build a serverless application using an API Gateway WebSocket API and AWS Lambda to send and receive messages to and from individual users or groups of users in a chat room\. Or you could invoke backend services such as AWS Lambda, Amazon Kinesis, or an HTTP endpoint based on message content\.

You can use API Gateway WebSocket APIs to build secure, real\-time communication applications without having to provision or manage any servers to manage connections or large\-scale data exchanges\. Targeted use cases include real\-time applications such as:
+ Chat applications
+ Real\-time dashboards such as stock tickers
+ Real\-time alerts and notifications

API Gateway provides WebSocket API management functionality such as:
+ Monitoring and throttling of connections and messages
+ Using AWS X\-Ray to trace messages as they travel through the APIs to backend services
+ Easy integration with HTTP/HTTPS endpoints

## Who Uses API Gateway?<a name="apigateway-who-uses-api-gateway"></a>

There are two kinds of developers who use API Gateway: API developers and app developers\.

An API developer creates and deploys an API to enable the required functionality in API Gateway\. The API developer must be an IAM user in the AWS account that owns the API\.

An app developer builds a functioning application to call AWS services by invoking a WebSocket or REST API created by an API developer in API Gateway\.

The app developer is the customer of the API developer\. The app developer does not need to have an AWS account, provided that the API either does not require IAM permissions or supports authorization of users through third\-party federated identity providers supported by [Amazon Cognito user pool identity federation](https://docs.aws.amazon.com/cognito/latest/developerguide/)\. Such identity providers include Amazon, Amazon Cognito User Pools, Facebook, and Google\.

### Creating and Managing an API Gateway API<a name="api-gateway-overview-developer-experience-managing-api"></a>

An API developer works with the API Gateway service component for API management, named `apigateway`, to create, configure, and deploy an API\. Each API includes a set of resources and methods\. A resource is a logical entity that an app can access through a resource path\. 

 As an API developer, you can create and manage an API by using the API Gateway console, described in [Getting Started with Amazon API Gateway](getting-started.md), or by calling the [API Gateway V1 and V2 API References](api-ref.md)\. There are several ways to call this API\. They include using the AWS Command\-Line Interface \(CLI\), or by using an AWS SDK\. In addition, you can enable API creation with [AWS CloudFormation templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html) or \(in the case of REST APIs\) [API Gateway Extensions to OpenAPI](api-gateway-swagger-extensions.md)\. For a list of regions where API Gateway is available, as well as the associated control service endpoints, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#apigateway_region)\. 

### Calling an API Gateway API<a name="api-gateway-overview-developer-experience-invoking-api"></a>

An app developer works with the API Gateway service component for API execution, named `execute-api`, to invoke an API that was created or deployed in API Gateway\. The underlying programming entities are exposed by the created API\. There are several ways to call such an API\. You can use the API Gateway console to test invoking the API\. For REST APIs, you can use a REST API client, such as [cURL](https://curl.haxx.se/) or [POSTMAN](https://www.getpostman.com/), or an SDK generated by API Gateway for the API to invoke the API\.