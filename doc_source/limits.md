# Amazon API Gateway quotas and important notes<a name="limits"></a>

**Topics**
+ [API Gateway quotas](#api-gateway-limits)
+ [Amazon API Gateway important notes](api-gateway-known-issues.md)

## API Gateway quotas<a name="api-gateway-limits"></a>

Unless noted otherwise, the quotas can be increased upon request\. To request a quota increase, you can use [Service Quotas](https://docs.aws.amazon.com/servicequotas/latest/userguide/) or contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

 When authorization is enabled on a method, the maximum length of the method's ARN \(for example, `arn:aws:execute-api:{region-id}:{account-id}:{api-id}/{stage-id}/{method}/{resource}/{path}`\) is 1600 bytes\. The path parameter values \(whose size is determined at runtime\) can cause the ARN length to exceed the limit\. When this happens, the API client receives a `414 Request URI too long` response\. 

**Note**  
This limits URI length when resource policies are used\. In the case of private APIs where a resource policy is required, this limits the URI length of all private APIs\.

### API Gateway account\-level quotas<a name="apigateway-account-level-limits-table"></a>

The following quotas apply at the account level per Region in Amazon API Gateway\.


| Resource or operation | Default quota | Can be increased | 
| --- | --- | --- | 
| Throttle quota per Region across HTTP APIs, REST APIs, WebSocket APIs, and WebSocket callback APIs | 10,000 requests per second \(RPS\) with an additional burst capacity provided by the [token bucket algorithm](https://en.wikipedia.org/wiki/Token_bucket), using a maximum bucket capacity of 5,000 requests\.  The burst quota is determined by the API Gateway service team based on the overall RPS quota for the account\. It is not a quota that a customer can control or request changes to\.  | Yes | 
| Regional APIs | 600 | No | 
| Edge\-optimized APIs | 120 | No | 

### HTTP API quotas<a name="http-api-quotas"></a>

The following quotas apply to configuring and running an HTTP API in API Gateway\.

#### <a name="http-api-quotas.table"></a>


| Resource or operation | Default quota | Can be increased | 
| --- | --- | --- | 
| Routes per API  | 300 | Yes | 
| Integrations per API | 300 | No | 
| Maximum integration timeout | 30 seconds | No | 
| Stages per API | 10 | Yes | 
| Tags per stage  | 50 | No | 
| Total combined size of request line and header values | 10240 bytes | No | 
| Payload size | 10 MB | No | 
| Custom domains per account per Region | 120 | Yes | 
| Access log template size  | 3 KB | No | 
| Amazon CloudWatch Logs log entry  | 1 MB | No | 
| Authorizers per API  | 10 | Yes | 
| Audiences per authorizer  | 50 | No | 
| Scopes per route  | 10 | No | 
| Timeout for JSON Web Key Set endpoint  | 1500 ms | No | 
| Timeout for OpenID Connect discovery endpoint  | 1500 ms | No | 
| VPC links per account per Region | 10 | Yes | 
| Subnets per VPC link  | 10 | Yes | 
| Stage variables per stage | 100 | No | 

### API Gateway quotas for configuring and running a WebSocket API<a name="apigateway-execution-service-websocket-limits-table"></a>

The following quotas apply to configuring and running a WebSocket API in Amazon API Gateway\.


| Resource or operation | Default quota | Can be increased | 
| --- | --- | --- | 
| New connections per second per account \(across all WebSocket APIs\) per Region | 500 | Yes | 
| AWS Lambda authorizers per API | 10 | Yes | 
| AWS Lambda authorizer result size | 8 KB | No | 
| Routes per API | 300 | Yes | 
| Integrations per API | 300 | Yes | 
| Stages per API | 10 | Yes | 
| WebSocket frame size | 32 KB | No | 
| Message payload size | 128 KB \* | No | 
| Connection duration for WebSocket API | 2 hours | No | 
| Idle Connection Timeout | 10 minutes | No | 

\* Because of the WebSocket frame\-size quota of 32 KB, a message larger than 32 KB must be split into multiple frames, each 32 KB or smaller\. If a larger message \(or larger frame size\) is received, the connection is closed with code 1009\.

### API Gateway quotas for configuring and running a REST API<a name="api-gateway-execution-service-limits-table"></a>

The following quotas apply to configuring and running a REST API in Amazon API Gateway\.


| Resource or operation | Default quota | Can be increased | 
| --- | --- | --- | 
| Custom domain names per account per Region | 120 | Yes | 
| Length, in characters, of the URL for an edge\-optimized API | 8192 | No | 
| Length, in characters, of the URL for a regional API | 10240 | No | 
| Private APIs per account per Region | 600 | No | 
| Length, in characters, of API Gateway resource policy | 8192 | Yes | 
| API keys per account per Region | 500 | Yes | 
| Client certificates per account per Region | 60 | Yes | 
| Authorizers per API \(AWS Lambda and Amazon Cognito\) | 10 | Yes | 
| Documentation parts per API | 2000 | Yes | 
| Resources per API | 300 | Yes | 
| Stages per API | 10 | Yes | 
| Stage variables per stage | 100 | No | 
| Length, in characters, of the key in a stage variable | 64 | No | 
| Length, in characters, of the value in a stage variable | 512 | No | 
| Usage plans per account per Region | 300 | Yes | 
| Usage plans per API key | 10 | Yes | 
| VPC links per account per Region | 20 | Yes | 
| API caching TTL | 300 seconds by default and configurable between 0 and 3600 by an API owner\. | Not for the upper bound \(3600\) | 
| Cached response size | 1048576 Bytes\. Cache data encryption may increase the size of the item that is being cached\. | No | 
| Integration timeout | 50 milliseconds \- 29 seconds for all integration types, including Lambda, Lambda proxy, HTTP, HTTP proxy, and AWS integrations\. | Not for the lower or upper bounds\. | 
| Total combined size of all header values | 10240 Bytes | No | 
| Payload size | 10 MB | No | 
| Tags per stage | 50 | No | 
| Number of iterations in a \#foreach \.\.\. \#end loop in mapping templates | 1000 | No | 
| ARN length of a method with authorization | 1600 bytes | No | 

For [restapi:import](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) or [restapi:put](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-put/), the maximum size of the API definition file is 6 MB\. 

All of the per\-API quotas can only be increased on specific APIs\.

### API Gateway quotas for creating, deploying and managing an API<a name="api-gateway-control-service-limits-table"></a>

The following fixed quotas apply to creating, deploying, and managing an API in API Gateway, using the AWS CLI, the API Gateway console, or the API Gateway REST API and its SDKs\. These quotas can't be increased\.


| Action | Default quota | Can be increased | 
| --- | --- | --- | 
| [CreateApiKey](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-create/) | 5 requests per second per account | No | 
| [CreateDeployment](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/) | 1 request every 5 seconds per account | No | 
| [CreateDocumentationVersion](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/documentationversion-create/) | 1 request every 20 seconds per account | No | 
| [CreateDomainName](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-create/) | 1 request every 30 seconds per account | No | 
| [CreateResource](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-create/) | 5 requests per second per account | No | 
| [CreateRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-create/) | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html)[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html) | No | 
|  [CreateVpcLink](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/vpclinks.html#CreateVpcLink) \(V2\)  | 1 request every 15 seconds per account | No | 
| [DeleteApiKey](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-delete/) | 5 requests per second per account | No | 
| [DeleteDomainName](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-delete/) | 1 request every 30 seconds per account | No | 
| [DeleteResource](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-delete/) | 5 requests per second per account | No | 
| [DeleteRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-delete/) | 1 request every 30 seconds per account | No | 
| [GetResources](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-resources/) | 5 requests every 2 seconds per account | No | 
|  [DeleteVpcLink](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/vpclinks.html#DeleteVpcLink) \(V2\)  | 1 request every 30 seconds per account | No | 
| [ImportDocumentationParts](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/documentationpart-import/) | 1 request every 30 seconds per account | No | 
| [ImportRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html)[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html) | No | 
| [PutRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-put/) | 1 request per second per account | No | 
| [UpdateAccount](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/account-update/) | 1 request every 20 seconds per account | No | 
| [UpdateDomainName](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-update/) | 1 request every 30 seconds per account | No | 
| [UpdateUsagePlan](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/usageplan-update/) | 1 request every 20 seconds per account | No | 
| Other operations | No quota up to the total account quota\. | No | 
| Total operations | 10 requests per second with a burst quota of 40 requests per second\. | No | 