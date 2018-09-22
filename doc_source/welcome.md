# What Is Amazon API Gateway?<a name="welcome"></a>

 Amazon API Gateway is an AWS service that enables developers to create, publish, maintain, monitor, and secure APIs at any scale\. You can create APIs that access AWS or other web services, as well as data stored in the [AWS Cloud](https://aws.amazon.com/what-is-cloud-computing/)\.

**Topics**
+ [Gateway to AWS Cloud and Beyond](#api-gateway-overview-aws-backbone)
+ [Developer Experiences](#api-gateway-overview-developer-experience)
+ [Benefits of API Gateway](#api-gateway-overview-benefits)
+ [Amazon API Gateway Concepts](api-gateway-basic-concept.md)
+ [API Gateway Pricing](api-gateway-pricing.md)

## Gateway to AWS Cloud and Beyond<a name="api-gateway-overview-aws-backbone"></a>

API Gateway can be considered a [backplane](https://en.wikipedia.org/wiki/Backplane) in the cloud to connect [AWS services](https://aws.amazon.com/) and other public or private websites\. It provides consistent RESTful application programming interfaces \(APIs\) for mobile and web applications to access AWS services\. 

The following diagram shows API Gateway architecture\. 

![\[API Gateway Architecture Diagram\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/BackplaneArch.png)

 In practical terms, API Gateway lets you create, configure, and host a [RESTful API](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/) to enable applications to access the AWS Cloud\. For example, an application can call an API in API Gateway to upload a user's annual income and expense data to [Amazon Simple Storage Service](https://docs.aws.amazon.com/AmazonS3/latest/dev/welcome.html) or [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/welcome.html), process the data in [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) to compute tax owed, and file a tax return via the IRS website\. 

As shown in the diagram, an app \(or client application\) gains programmatic access to AWS services, or a website on the internet, through one or more APIs, which are hosted in API Gateway\. The app is at the API's frontend\. The integrated AWS services and websites are located at the API's backend\. In API Gateway, the frontend is encapsulated by [method requests](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/) and [method responses](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/), and the backend is encapsulated by [integration requests](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) and [integration responses](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/)\. 

With Amazon API Gateway, you can build an API to provide your users with an integrated and consistent developer experience to build AWS cloud\-based applications\. 

### Part of AWS Serverless Infrastructure<a name="api-gateway-overview-a-serverless-pillar"></a>

 Together with AWS Lambda, API Gateway forms the app\-facing part of the AWS serverless infrastructure\. For an app to call publicly available AWS services, you can use Lambda to interact with the required services and expose the Lambda functions through API methods in API Gateway\. AWS Lambda runs the code on a highly available computing infrastructure\. It performs the necessary execution and administration of the computing resources\. To enable the serverless applications, API Gateway supports [streamlined proxy integrations](api-gateway-set-up-simple-proxy.md) with AWS Lambda and HTTP endpoints\. 

## Developer Experiences<a name="api-gateway-overview-developer-experience"></a>

There are two kinds of developers who use API Gateway: app developers and API developers\.

An app developer builds a functioning application to call AWS services by invoking API methods in API Gateway\.

An API developer creates and deploys an API to enable the required functionality in API Gateway\. The API developer must be an IAM user in the AWS account that owns the API\.

The app developer does not need to have an AWS account, provided that the API either does not require IAM permissions or supports authorization of users through third\-party identity providers supported by [Amazon Cognito identity federation](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html)\. Such identity providers include Amazon, Amazon Cognito User Pools, Facebook, and Google\.

### Creating and Managing an API Gateway API<a name="api-gateway-overview-developer-experience-managing-api"></a>

An API developer works with the API Gateway service component for API management, named `apigateway`, to create, configure, and deploy an API\. Each API includes a set of resources and methods\. A resource is a logical entity that an app can access through a resource path\. 

For example, `/incomes` could be the path of a resource representing the income of the app user\. A resource can have one or more operations that are defined by appropriate HTTP verbs such as GET, POST, PUT, PATCH, and DELETE\. A combination of a resource path and an operation identifies a method of the API\. For example, a `POST /incomes` method could add an income earned by the caller, and a `GET /expenses` method could query the reported expenses incurred by the caller\. 

A method corresponds to a REST API request that is submitted by the user of your API and the response returned to the user\. The app does not need to know where the requested data is stored and fetched from on the backend\. The API interfaces with the backend by means of *integration requests* and *integration responses*\. 

For example, with DynamoDB as the backend, the API developer sets up the integration request to forward the incoming method request to the chosen backend\. The setup includes specifications of an appropriate DynamoDB action, required IAM role and policies, and required input data transformation\. The backend returns the result to API Gateway as an integration response\. To route the integration response to an appropriate method response \(of a given HTTP status code\) to the client, you can configure the integration response to map required response parameters from integration to method\. You then translate the output data format of the backend to that of the frontend, if necessary\. API Gateway enables you to define a schema or model for the [payload ](https://en.wikipedia.org/wiki/Payload_(computing)) to facilitate setting up the body mapping template\.

 As an API developer, you can create and manage an API by using the API Gateway console, described in [Getting Started with Amazon API Gateway](getting-started.md), or by calling the [API Gateway REST API](api-ref.md)\. There are several ways to call this API\. They include using the AWS Command\-Line Interface \(CLI\), or by using an AWS SDK\. You can also use a REST API client, such as Postman, to make raw API calls\. In addition, you can enable API creation with [AWS CloudFormation templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html) or [API Gateway Extensions to Swagger](api-gateway-swagger-extensions.md)\. For a list of regions where API Gateway is available, as well as the associated control service endpoints, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#apigateway_region)\. 

### Calling an API Gateway API<a name="api-gateway-overview-developer-experience-invoking-api"></a>

An app developer works with the API Gateway service component for API execution, named `execute-api`, to invoke an API that was created or deployed in API Gateway\. The underlying programming entities are exposed by the created API\. There are several ways to call such an API\. You can use the API Gateway console to test invoking the API\. You can use a REST API client, such as CURL or Postman, or an SDK generated by API Gateway for the API to invoke the API\.

Be aware of the differences between the `apigateway` and `execute-api` API Gateway service components\. Reference the appropriate service component name when you select one while, for example, setting IAM permission policies for building or calling an API\.

## Benefits of API Gateway<a name="api-gateway-overview-benefits"></a>

API Gateway helps you deliver robust, secure, and scalable mobile and web application backends\. API Gateway allows you to securely connect mobile and web applications to business logic hosted on AWS Lambda, APIs hosted on Amazon EC2, or other publicly addressable web services hosted inside or outside of AWS\. With API Gateway, you can create and operate APIs for backend services\. For example, you don't need to develop and maintain infrastructure to handle authorization and access control, traffic management, monitoring and analytics, version management, and software development kit \(SDK\) generation\.

 API Gateway is designed for web and mobile developers who want to provide secure, reliable access to backend APIs for access from mobile apps, web apps, and server apps that are built internally or by third\-party ecosystem partners\. The business logic behind the APIs can be provided by a publicly accessible endpoint that API Gateway proxies call, or it can be entirely run as a Lambda function\. 