# Amazon API Gateway Concepts<a name="api-gateway-basic-concept"></a>

**API Gateway**  <a name="apigateway-definition-apigateway"></a>
API Gateway is an AWS service that supports the following:  
+ Creating, deploying, and managing a [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) application programming interface \(API\) to expose backend HTTP endpoints, AWS Lambda functions, or other AWS services\.
+ Creating, deploying, and managing a [WebSocket](https://tools.ietf.org/html/rfc6455) API to expose AWS Lambda functions or other AWS services\.
+ Invoking exposed API methods through the frontend HTTP and WebSocket endpoints\.

**API Gateway REST API**  <a name="apigateway-definition-apigateway-api"></a>
A collection of HTTP resources and methods that are integrated with backend HTTP endpoints, Lambda functions, or other AWS services\. This collection can be deployed in one or more stages\. Typically, API resources are organized in a resource tree according to the application logic\. Each API resource can expose one or more API methods that have unique HTTP verbs supported by API Gateway\.

**API Gateway WebSocket API**  <a name="apigateway-definition-apigateway-websocket-api"></a>
A collection of WebSocket routes and route keys that are integrated with backend HTTP endpoints, Lambda functions, or other AWS services\. The collection can be deployed in one or more stages\. API methods are invoked through frontend WebSocket connections that you can associate with a registered custom domain name\.

**API deployment**  <a name="apigateway-definition-api-deployment"></a>
A point\-in\-time snapshot of your API Gateway API\. To be available for clients to use, the deployment must be associated with one or more API stages\.

**API developer**  <a name="apigateway-definition-api-developer"></a>
Your AWS account that owns an API Gateway deployment \(for example, a service provider that also supports programmatic access\.\)

**API endpoint**  <a name="apigateway-definition-api-endpoints"></a>
A hostname for an API in API Gateway that is deployed to a specific region\. The hostname is of the form `{api-id}.execute-api.{region}.amazonaws.com`\. The following types of API endpoints are supported:  
+ [Edge\-optimized API endpoint](#apigateway-definition-edge-optimized-api-endpoint)
+ [Private API endpoint](#apigateway-definition-private-api-endpoint)
+ [Regional API endpoint](#apigateway-definition-regional-api-endpoint)

**API key**  <a name="apigateway-definition-api-key"></a>
An alphanumeric string that API Gateway uses to identify an app developer who uses your REST or WebSocket API\. API Gateway can generate API keys on your behalf, or you can import them from a CSV file\. You can use API keys together with [Lambda authorizers](apigateway-use-lambda-authorizer.md) or [usage plans](api-gateway-api-usage-plans.md) to control access to your APIs\.  
See [API endpoints](#apigateway-definition-api-endpoints)\.

**API owner**  <a name="apigateway-definition-api-owner"></a>
See [API developer](#apigateway-definition-api-developer)\.

**API stage**  <a name="apigateway-definition-api-stage"></a>
A logical reference to a lifecycle state of your REST or WebSocket API \(for example, 'dev', 'prod', 'beta', 'v2'\)\. API stages are identified by API ID and stage name\.

**App developer**  <a name="apigateway-definition-app-developer"></a>
An app creator who may or may not have an AWS account and interacts with the API that you, the API developer, have deployed\. App developers are your customers\. An app developer is typically identified by an [API key](#apigateway-definition-api-key)\.

**Callback URL**  
When a new client is connected to via a WebSocket connection, you can call an integration in API Gateway to store the client's callback URL\. You can then use that callback URL to send messages to the client from the backend system\.

**Developer portal**  <a name="apigateway-definition-developer-portal"></a>
An application that allows your customers to register, discover, and subscribe to your API products \(API Gateway usage plans\), manage their API keys, and view their usage metrics for your APIs\.

**Edge\-optimized API endpoint**  <a name="apigateway-definition-edge-optimized-api-endpoint"></a>
The default host name of an API Gateway API that is deployed to the specified region while using a CloudFront distribution to facilitate client access typically from across AWS regions\. API requests are routed to the nearest CloudFront Point of Presence \(POP\), which typically improves connection time for geographically diverse clients\.  
See [API endpoints](#apigateway-definition-api-endpoints)\.

**Integration request**  <a name="apigateway-definition-integration-request"></a>
The internal interface of a WebSocket API route or REST API method in API Gateway, in which you map the body of a route request or the parameters and body of a method request to the formats required by the backend\.

**Integration response**  <a name="apigateway-definition-integration-response"></a>
The internal interface of a WebSocket API route or REST API method in API Gateway, in which you map the status codes, headers, and payload that are received from the backend to the response format that is returned to a client app\.

**Mapping template**  <a name="apigateway-definition-mapping-template"></a>
A scripts in [Velocity Template Language \(VTL\)](http://velocity.apache.org/engine/devel/vtl-reference.html) that transforms a request body from the frontend data format to the backend data format, or that transforms a response body from the backend data format to the frontend data format\. Mapping templates can be specified in the integration request or in the integration response\. They can reference data made available at run time as context and stage variables\. The mapping can be as simple as an [identity transform](https://en.wikipedia.org/wiki/Identity_transform) that passes the headers or body through the integration as\-is from the client to the backend for a request\. The same is true for a response, in which the payload is passed from the backend to the client\.

**Method request**  <a name="apigateway-definition-method-request"></a>
The public interface of a REST API method in API Gateway that defines the parameters and body that an app developer must send in requests to access the backend through the API\.

**Method response**  <a name="apigateway-definition-method-response"></a>
The public interface of a REST API that defines the status codes, headers, and body models that an app developer should expect in responses from the API\. 

**Mock integration**  <a name="apigateway-definition-mock-integration"></a>
In a mock integration, API responses are generated from API Gateway directly, without the need for an integration backend\. As an API developer, you decide how API Gateway responds to a mock integration request\. For this, you configure the method's integration request and integration response to associate a response with a given status code\.

**Model**  <a name="apigateway-definition-model"></a>
A data schema specifying the data structure of a request or response payload\. A model is required for generating a strongly typed SDK of an API\. It is also used to validate payloads\. A model is convenient for generating a sample mapping template to initiate creation of a production mapping template\. Although useful, a model is not required for creating a mapping template\.

**Private API**  <a name="apigateway-definition-private-api"></a>
See [Private API endpoint](#apigateway-definition-private-api)\.

**Private API endpoint**  <a name="apigateway-definition-private-api-endpoint"></a>
An API endpoint that is exposed through interface VPC endpoints and allows a client to securely access private API resources inside a VPC\. Private APIs are isolated from the public internet, and they can only be accessed using VPC endpoints for API Gateway that have been granted access\.

**Private integration**  <a name="apigateway-definition-private-integration"></a>
An API Gateway integration type for a client to access resources inside a customer's VPC through a private REST API endpoint without exposing the resources to the public internet\.

**Proxy integration**  <a name="apigateway-definition-proxy-integration"></a>
A simplified API Gateway integration configuration for a REST or WebSocket API\. You can set up a proxy integration as an HTTP proxy integration or a Lambda proxy integration\. For HTTP proxy integration, API Gateway passes the entire request and response between the frontend and an HTTP backend\. For Lambda proxy integration, API Gateway sends the entire request as input to a backend Lambda function\. API Gateway then transforms the Lambda function output to a frontend HTTP response\. In REST APIs, proxy integration is most commonly used with a proxy resource, which is represented by a greedy path variable \(e\.g\., `{proxy+}`\) combined with a catch\-all `ANY` method\.

**Regional API endpoint**  <a name="apigateway-definition-regional-api-endpoint"></a>
The host name of an API that is deployed to the specified region and intended to serve clients, such as EC2 instances, in the same AWS region\. API requests are targeted directly to the region\-specific API Gateway without going through any CloudFront distribution\. For in\-region requests, a regional endpoint bypasses the unnecessary round trip to a CloudFront distribution\. In addition, you can apply [latency\-based routing](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html#routing-policy-latency) on regional endpoints to deploy an API to multiple regions using the same regional API endpoint configuration, set the same custom domain name for each deployed API, and configure latency\-based DNS records in Route 53 to route client requests to the region that has the lowest latency\.  
See [API endpoints](#apigateway-definition-api-endpoints)\.

**Route**  <a name="apigateway-definition-route"></a>
A WebSocket route in API Gateway is used to direct incoming messages to a specific integration, such as an AWS Lambda function, based on the content of the message\. When you define your WebSocket API, you specify a route key and an integration backend\. The route key is an attribute in the message body\. When the route key is matched in an incoming message, the integration backend is invoked\. A default route can also be set for non\-matching route keys or to specify a proxy model that passes the message through as\-is to backend components that perform the routing and process the request\.

**Route request**  <a name="apigateway-definition-route-request"></a>
The public interface of a WebSocket API method in API Gateway that defines the body that an app developer must send in the requests to access the backend through the API\.

**Route response**  <a name="apigateway-definition-route-response"></a>
The public interface of a WebSocket API that defines the status codes, headers, and body models that an app developer should expect from API Gateway\.

**Usage plan**  <a name="apigateway-definition-usage-plan"></a>
A [usage plan](api-gateway-api-usage-plans.md) provides selected API clients with access to one or more deployed REST or WebSocket APIs\. You can use a usage plan to configure throttling and quota limits, which are enforced on individual client API keys\.

**WebSocket connection**  
API Gateway maintains a persistent connection between clients and API Gateway itself\. There is no persistent connection between API Gateway and backend integrations such as Lambda functions; backend services are invoked as needed, based on the content of messages received from clients\.