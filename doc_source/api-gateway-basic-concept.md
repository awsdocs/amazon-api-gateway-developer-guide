# Amazon API Gateway Concepts<a name="api-gateway-basic-concept"></a>

**API Gateway**  <a name="apigateway-definition-apigateway"></a>
API Gateway is an AWS service that supports the following:  

1. Creating, deploying, and managing a RESTful application programming interface \(API\) to expose backend HTTP endpoints, AWS Lambda functions, or other AWS services\.

1. Invoking exposed API methods through the frontend HTTP endpoints\.

**API Gateway API**  <a name="apigateway-definition-apigateway-api"></a>
 A collection of resources and methods that are integrated with backend HTTP endpoints, Lambda functions, or other AWS services\. The collection can be deployed in one or more stages\. API methods are invoked through frontend HTTP endpoints that you can associate with a registered custom domain name\. Permissions to invoke a method are granted using IAM roles and policies or API Gateway Lambda authorizers \(formerly known as custom authorizers\)\. An API can present a certificate to be authenticated by the backend\. Typically, API resources are organized in a resource tree according to the application logic\. Each API resource can expose one or more API methods that must have unique HTTP verbs supported by API Gateway\. 

**API deployment**  <a name="apigateway-definition-api-deployment"></a>
A point\-in\-time snapshot of your API Gateway API resources and methods\. To be available for clients to use, the deployment must be associated with one or more API stages\.

**API developer**  <a name="apigateway-definition-api-developer"></a>
Your AWS account that owns an API Gateway deployment \(for example, a service provider that also supports programmatic access\.\)

**API endpoints**  <a name="apigateway-definition-api-endpoints"></a>
Host names APIs in API Gateway, which are deployed to a specific region and of the `{rest-api-id}.execute-api.{region}.amazonaws.com` format\. The following types of API endpoints are supported:   
+ *Edge\-optimized API endpoint:* The default host name of an API Gateway API that is deployed to the specified region while using a CloudFront distribution to facilitate client access typically from across AWS regions\. API requests are routed to the nearest CloudFront Point of Presence \(POP\) which typically improves connection time for geographically diverse clients\. An API is edge\-optimized if you do not explicitly specify its endpoint type when creating the API\. 
+ *Regional API endpoint:* The host name of an API that is deployed to the specified region and intended to serve clients, such as EC2 instances, in the same AWS region\. API requests are targeted directly to the region\-specific API Gateway without going through any CloudFront distribution\. For in\-region requests, a regional endpoint bypasses the unnecessary round trip to a CloudFront distribution\. In addition, you can apply [latency\-based routing](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html#routing-policy-latency) on regional endpoints to deploy an API to multiple regions using the same regional API endpoint configuration, set the same custom domain name for each deployed API, and configure latency\-based DNS records in Route 53 to route client requests to the region that has the lowest latency\. 
+ *Private API endpoint:* Allows a client to securely access private API resources inside a VPC\. Private APIs are isolated from the public Internet, and they can only be accessed using VPC endpoints for API Gateway that have been granted access\.

**API key**  <a name="apigateway-definition-api-key"></a>
An alphanumeric string that API Gateway uses to identify an app developer who uses your API\. API Gateway can generate API keys on your behalf, or you can import them from a CSV file\. You can use API keys together with [Lambda authorizers](apigateway-use-lambda-authorizer.md) or [usage plans](api-gateway-api-usage-plans.md) to control access to your APIs\.

**API owner**  <a name="apigateway-definition-api-owner"></a>
See [API developer](#apigateway-definition-api-developer)\.

**API stage**  <a name="apigateway-definition-api-stage"></a>
A logical reference to a lifecycle state of your API \(for example, 'dev', 'prod', 'beta', 'v2'\)\. API stages are identified by API ID and stage name\.

**App developer**  <a name="apigateway-definition-app-developer"></a>
 An app creator who may or may not have an AWS account and interacts with the API that you, the API developer, have deployed\. App developers are your customers\. An app developer is typically identified by an [API key](#apigateway-definition-api-key)\. 

**Integration request**  <a name="apigateway-definition-integration-request"></a>
The internal interface of an API method in API Gateway, in which you map the parameters and body of a method request to the formats required by the backend\. 

**Integration response**  <a name="apigateway-definition-integration-response"></a>
The internal interface of an API method in API Gateway, in which you map the status codes, headers, and payload that are received from the backend to the response format that is returned to a client app\. 

**Mapping template**  <a name="apigateway-definition-mapping-template"></a>
Scripts in [Velocity Template Language \(VTL\)](http://velocity.apache.org/engine/devel/vtl-reference.html) to transform a request body from the frontend data format to the backend data format, or to transform a response body from the backend data format to the frontend data format\. Mapping templates are specified in the integration request or integration response\. They can reference data made available at run time as context and stage variables\. An identity transformation is referred to as a passthrough\. In a passthrough, a payload is passed as\-is from the client to the backend for a request\. For a response, the payload is passed from the backend to the client\.

**Method request**  <a name="apigateway-definition-method-request"></a>
 The public interface of an API method in API Gateway that defines the parameters and body that an app developer must send in the requests to access the backend through the API\. 

**Method response**  <a name="apigateway-definition-method-response"></a>
 The public interface of an API that defines the status codes, headers, and body models that an app developer should expect from API Gateway\. 

**Model**  <a name="apigateway-definition-model"></a>
Data schema specifying the data structure of a request or response payload\. It is required for generating a strongly typed SDK of an API\. It is also used to validate payload\. A model is convenient for generating a sample mapping template to initiate creation of a production mapping template\. Although useful, a model is not required for creating a mapping template\.

**Private API**  <a name="apigateway-definition-private-api"></a>
An API that is exposed through interface VPC endpoints and isolated from the public internet

**Private integration**  <a name="apigateway-definition-private-integration"></a>
An API Gateway integration type for a client to access resources inside a customer's VPC through a private API endpoint without exposing the resources to the public internet\.

**Proxy integration**  <a name="apigateway-definition-proxy-integration"></a>
A simplified API Gateway integration configuration\. You can set up a proxy integration as an HTTP proxy integration type or a Lambda proxy integration type\. For the HTTP proxy integration, API Gateway passes the entire request and response between the frontend and an HTTP backend\. For the Lambda proxy integration, API Gateway sends the entire request as an input to a backend Lambda function\. API Gateway then transforms the Lambda function output to a frontend HTTP response\. The proxy integration is most commonly used with a proxy resource, which is represented by a greedy path variable \(e\.g\., `{proxy+}`\) combined with a catch\-all `ANY` method\.

**Usage plan**  <a name="apigateway-definition-usage-plan"></a>
A [usage plan](api-gateway-api-usage-plans.md) provides selected API clients with access to one or more deployed APIs\. You can use a usage plan to configure throttling and quota limits, which are enforced on individual client API keys\.