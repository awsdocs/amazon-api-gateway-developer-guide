# What is Amazon API Gateway?<a name="welcome"></a>

Amazon API Gateway is an AWS service for creating, publishing, maintaining, monitoring, and securing REST, HTTP, and WebSocket APIs at any scale\. API developers can create APIs that access AWS or other web services, as well as data stored in the [AWS Cloud](https://aws.amazon.com/what-is-cloud-computing/)\. As an API Gateway API developer, you can create APIs for use in your own client applications\. Or you can make your APIs available to third\-party app developers\. For more information, see [Who uses API Gateway?](api-gateway-overview-developer-experience.md#apigateway-who-uses-api-gateway)\.

API Gateway creates RESTful APIs that:
+ Are HTTP\-based\.
+ Enable stateless client\-server communication\.
+ Implement standard HTTP methods such as GET, POST, PUT, PATCH, and DELETE\.

For more information about API Gateway REST APIs and HTTP APIs, see [Choosing between HTTP APIs and REST APIs](http-api-vs-rest.md), [Working with HTTP APIs](http-api.md), [Use API Gateway to create REST APIs](api-gateway-overview-developer-experience.md#api-gateway-overview-rest), and [Creating a REST API in Amazon API Gateway](how-to-create-api.md)\.

API Gateway creates WebSocket APIs that:
+ Adhere to the [WebSocket](https://tools.ietf.org/html/rfc6455) protocol, which enables stateful, full\-duplex communication between client and server\.
+ Route incoming messages based on message content\.

For more information about API Gateway WebSocket APIs, see [Use API Gateway to create WebSocket APIs](api-gateway-overview-developer-experience.md#api-gateway-overview-websocket) and [About WebSocket APIs in API Gateway](apigateway-websocket-api-overview.md)\.

**Topics**
+ [Architecture of API Gateway](#api-gateway-overview-aws-backbone)
+ [Features of API Gateway](#api-gateway-overview-features)
+ [API Gateway use cases](api-gateway-overview-developer-experience.md)
+ [Accessing API Gateway](#introduction-accessing-apigateway)
+ [Part of AWS serverless infrastructure](#api-gateway-overview-a-serverless-pillar)
+ [How to get started with Amazon API Gateway](#welcome-how-to-get-started)
+ [Amazon API Gateway concepts](api-gateway-basic-concept.md)
+ [Choosing between HTTP APIs and REST APIs](http-api-vs-rest.md)
+ [API Gateway pricing](api-gateway-pricing.md)

## Architecture of API Gateway<a name="api-gateway-overview-aws-backbone"></a>

The following diagram shows API Gateway architecture\.

![\[API Gateway architecture diagram\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/Product-Page-Diagram_Amazon-API-Gateway-How-Works.png)

This diagram illustrates how the APIs you build in Amazon API Gateway provide you or your developer customers with an integrated and consistent developer experience for building AWS serverless applications\. API Gateway handles all the tasks involved in accepting and processing up to hundreds of thousands of concurrent API calls\. These tasks include traffic management, authorization and access control, monitoring, and API version management\. 

API Gateway acts as a "front door" for applications to access data, business logic, or functionality from your backend services, such as workloads running on Amazon Elastic Compute Cloud \(Amazon EC2\), code running on AWS Lambda, any web application, or real\-time communication applications\.

## Features of API Gateway<a name="api-gateway-overview-features"></a>

Amazon API Gateway offers features such as the following:
+ Support for stateful \([WebSocket](apigateway-websocket-api.md)\) and stateless \([HTTP](http-api.md) and [REST](apigateway-rest-api.md)\) APIs\.
+ Powerful, flexible [authentication](apigateway-control-access-to-api.md) mechanisms, such as AWS Identity and Access Management policies, Lambda authorizer functions, and Amazon Cognito user pools\.
+ [Developer portal](apigateway-developer-portal.md) for publishing your APIs\.
+ [Canary release deployments](canary-release.md) for safely rolling out changes\.
+ [CloudTrail](cloudtrail.md) logging and monitoring of API usage and API changes\.
+ CloudWatch access logging and execution logging, including the ability to set alarms\. For more information, see [Monitoring REST API execution with Amazon CloudWatch metrics](monitoring-cloudwatch.md) and [Monitoring WebSocket API execution with CloudWatch metrics](apigateway-websocket-api-logging.md)\.
+ Ability to use AWS CloudFormation templates to enable API creation\. For more information, see [Amazon API Gateway Resource Types Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-reference-apigateway.html) and [Amazon API Gateway V2 Resource Types Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-reference-apigatewayv2.html)\.
+ Support for [custom domain names](how-to-custom-domains.md)\.
+ Integration with [AWS WAF](apigateway-control-access-aws-waf.md) for protecting your APIs against common web exploits\.
+ Integration with [AWS X\-Ray](apigateway-xray.md) for understanding and triaging performance latencies\.

For a complete list of API Gateway feature releases, see [Document history](history.md)\.

## Accessing API Gateway<a name="introduction-accessing-apigateway"></a>

You can access Amazon API Gateway in the following ways:
+ **AWS Management Console** – The procedures throughout this guide explain how to use the AWS Management Console to perform tasks\.
+ **AWS SDKs** – If you're using a programming language that AWS provides an SDK for, you can use an SDK to access API Gateway\. SDKs simplify authentication, integrate easily with your development environment, and provide access to API Gateway commands\. For more information, see [Tools for Amazon Web Services](https://aws.amazon.com/tools)\.
+ **API Gateway V1 and V2 APIs** – If you're using a programming language that an SDK isn't available for, see the [Amazon API Gateway Version 1 API Reference](https://docs.aws.amazon.com/apigateway/api-reference/) and [Amazon API Gateway Version 2 API Reference](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/api-reference.html)\.
+ **AWS Command Line Interface** – For more information, see [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/) in the *AWS Command Line Interface User Guide*\.
+ **AWS Tools for Windows PowerShell** – For more information, see [Setting Up the AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/) in the *AWS Tools for Windows PowerShell User Guide*\.

## Part of AWS serverless infrastructure<a name="api-gateway-overview-a-serverless-pillar"></a>

Together with [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/), API Gateway forms the app\-facing part of the AWS serverless infrastructure\. 

For an app to call publicly available AWS services, you can use Lambda to interact with required services and expose Lambda functions through API methods in API Gateway\. AWS Lambda runs your code on a highly available computing infrastructure\. It performs the necessary execution and administration of computing resources\. To enable serverless applications, API Gateway supports [streamlined proxy integrations](api-gateway-set-up-simple-proxy.md) with AWS Lambda and HTTP endpoints\. 

## How to get started with Amazon API Gateway<a name="welcome-how-to-get-started"></a>

For a quick introduction to Amazon API Gateway, see the following topics:
+ [Getting started with Amazon API Gateway](getting-started.md), which provides walkthroughs and videos that tell how to create APIs using Amazon API Gateway\.
+ [Announcing WebSocket APIs in Amazon API Gateway](https://aws.amazon.com/blogs/compute/announcing-websocket-apis-in-amazon-api-gateway/), which tells how to create a WebSocket API\.