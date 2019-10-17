# Amazon API Gateway Limits and Important Notes<a name="limits"></a>

**Topics**
+ [API Gateway Limits](#api-gateway-limits)
+ [Amazon API Gateway Important Notes](api-gateway-known-issues.md)

## API Gateway Limits<a name="api-gateway-limits"></a>

Unless noted otherwise, the limits can be increased upon request\. To request a limit increase, contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

 When authorization is enabled on a method, the maximum length of the method's ARN \(e\.g\., `arn:aws:execute-api:{region-id}:{account-id}:{api-id}/{stage-id}/{method}/{resource}/{path}`\) is 1600 bytes\. The path parameter values, the size of which are determined at run time, can cause the ARN length to exceed the limit\. When this happens, the API client will receive a `414 Request URI too long` response\. 

**Note**  
This limits URI length when resource policies are used\. In the case of private APIs where a resource policy is required, this limits the URI length of all private APIs\.

### API Gateway Account\-Level Limits<a name="apigateway-account-level-limits-table"></a>

The following limits apply at the account level in Amazon API Gateway\.


| Resource or Operation | Default Limit | Can Be Increased | 
| --- | --- | --- | 
| Throttle limit per region across REST APIs, WebSocket APIs, and WebSocket callback APIs | 10,000 requests per second \(RPS\) with an additional burst capacity provided by the [token bucket algorithm](https://en.wikipedia.org/wiki/Token_bucket), using a maximum bucket capacity of 5,000 requests\.  The burst limit is determined by the API Gateway service team based on the overall RPS limits for the account\. It is not a limit that a customer can control or request changes to\.  | Yes | 
| Regional APIs | 600 | No | 
| Edge\-Optimized APIs | 120 | No | 

### API Gateway Limits for Configuring and Running a WebSocket API<a name="apigateway-execution-service-websocket-limits-table"></a>

The following limits apply to configuring and running a WebSocket API in Amazon API Gateway\.


| Resource or Operation | Default Limit | Can Be Increased | 
| --- | --- | --- | 
| New connections per second per account \(across all WebSocket APIs\) per region | 500 | Yes | 
| AWS Lambda authorizers per API | 10 | Yes | 
| AWS Lambda authorizer result size | 8 KB | No | 
| Routes per API | 300 | Yes | 
| Integrations per API | 300 | Yes | 
| Stages per API | 10 | Yes | 
| WebSocket frame size | 32 KB | No | 
| Message payload size | 128 KB  Because of the WebSocket frame\-size limit of 32 KB, a message larger than 32 KB must be split into multiple frames, each 32 KB or smaller\. If a larger message \(or larger frame size\) is received, the connection is closed with code 1009\.  | No | 
| Connection duration for WebSocket API | 2 hours | No | 
| Idle Connection Timeout | 10 minutes | No | 

### API Gateway Limits for Configuring and Running a REST API<a name="api-gateway-execution-service-limits-table"></a>

The following limits apply to configuring and running a REST API in Amazon API Gateway\.


| Resource or Operation | Default Limit | Can Be Increased | 
| --- | --- | --- | 
| Custom domain names per account per region | 120 | Yes | 
| Length, in characters, of the URL for an edge\-optimized API | 8192 | No | 
| Length, in characters, of the URL for a regional API | 10240 | No | 
| Private APIs per account per region | 600 | No | 
| Length, in characters, of API Gateway resource policy | 8192 | Yes | 
| API keys per account per region | 500 | Yes | 
| Client certificates per account per region | 60 | Yes | 
| Authorizers per API \(AWS Lambda and Amazon Cognito\) | 10 | Yes | 
| Documentation parts per API | 2000 | Yes | 
| Resources per API | 300 | Yes | 
| Stages per API | 10 | Yes | 
| Stage variables per stage | 100 | Yes | 
| Length, in characters, of the key in a stage variable | 64 | Yes | 
| Length, in characters, of the value in a stage variable | 512 | Yes | 
| Usage plans per account per region | 300 | Yes | 
| Usage plans per API key | 10 | Yes | 
| Per\-method throttling limit settings per API stage | 20 | Yes | 
| VPC links per account per region | 20 | Yes | 
| API caching TTL | 300 seconds by default and configurable between 0 and 3600 by an API owner\. | Not for the upper bound \(3600\) | 
| Cached response size | 1048576 Bytes\. Cache data encryption may increase the size of the item that is being cached\. | No | 
| Integration timeout | 50 milliseconds \- 29 seconds for all integration types, including Lambda, Lambda proxy, HTTP, HTTP proxy, and AWS integrations\. | Not for the lower or upper bounds\. | 
| Total combined size of all header values | 10240 Bytes | No | 
| Payload size | 10 MB | No | 
| Tags per stage | 50 | No | 
| Number of iterations in a \#foreach \.\.\. \#end loop in mapping templates | 1000 | No | 
| ARN length of a method with authorization | 1600 bytes | No | 

For [restapi:import](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) or [restapi:put](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-put/), the maximum size of the API definition file is 2 MB\. 

All of the per\-API limits can only be increased on specific APIs\.

### API Gateway Limits for Creating, Deploying and Managing an API<a name="api-gateway-control-service-limits-table"></a>

The following fixed limits apply to creating, deploying, and managing an API in API Gateway, using the AWS CLI, the API Gateway console, or the API Gateway REST API and its SDKs\. These limits cannot be increased\.


| Action | Default Limit | Can Be Increased | 
| --- | --- | --- | 
| [CreateApiKey](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-create/) | 5 requests per second per account | No | 
| [CreateDeployment](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/) | 1 request every 5 seconds per account | No | 
| [CreateDocumentationVersion](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/documentationversion-create/) | 1 request every 20 seconds per account | No | 
| [CreateDomainName](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-create/) | 1 request every 30 seconds per account | No | 
| [CreateResource](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-create/) | 5 requests per second per account | No | 
| [CreateRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-create/) | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html)[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html) | No | 
| [DeleteApiKey](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-delete/) | 5 requests per second per account | No | 
| [DeleteDomainName](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-delete/) | 1 request every 30 seconds per account | No | 
| [DeleteResource](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-delete/) | 5 requests per second per account | No | 
| [DeleteRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-delete/) | 1 request every 30 seconds per account | No | 
| [GetResources](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-resources/) | 5 requests every 2 seconds per account | No | 
| [ImportDocumentationParts](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/documentationpart-import/) | 1 request every 30 seconds per account | No | 
| [ImportRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html)[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html) | No | 
| [PutRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-put/) | 1 request per second per account | No | 
| [UpdateAccount](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/account-update/) | 1 request every 20 seconds per account | No | 
| [UpdateDomainName](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-update/) | 1 request every 30 seconds per account | No | 
| [UpdateUsagePlan](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/usageplan-update/) | 1 request every 20 seconds per account | No | 
| Other operations | No limit up to the total account limit\. | No | 
| Total operations | 10 requests per second with a burst limit of 40 requests per second\. | No | 