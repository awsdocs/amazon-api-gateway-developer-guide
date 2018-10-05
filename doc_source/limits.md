# Amazon API Gateway Limits and Known Issues<a name="limits"></a>

**Topics**
+ [API Gateway Limits](#api-gateway-limits)
+ [Known Issues](api-gateway-known-issues.md)

## API Gateway Limits<a name="api-gateway-limits"></a>

Unless noted otherwise, the limits can be increased upon request\. To request a limit increase, contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

 When authorization is enabled on a method, the maximum length of the method's ARN \(e\.g\., `arn:aws:execute-api:{region-id}:{account-id}:{api-id}/{stage-id}/{method}/{resource}/{path}`\) is 1600 bytes\. The path parameter values, the size of which are determined at run time, can cause the ARN length to exceed the limit\. When this happens, the API client will receive a `414 Request URI too long` response\. 

 Header values are limited to 10240 bytes\. 

### API Gateway Limits for Configuring and Running an API<a name="api-gateway-execution-service-limits-table"></a>

The following limits apply to configuring and running an API in Amazon API Gateway\.


| Resource or Operation | Default Limit | Can Be Increased | 
| --- | --- | --- | 
| Throttle limit per account per region | 10000 requests per second \(RPS\) with an additional burst capacity provided by the [token bucket algorithm](https://en.wikipedia.org/wiki/Token_bucket), using a maximum bucket capacity of 5000 requests\.  The burst limit is determined by the API Gateway service team based on the overall RPS limits for the account\. It is not a limit that a customer can control or request changes to\.  | Yes | 
| Maximum number of Regional APIs per account per region | 600 | No | 
| Maximum number of Private APIs per account per region | 600 | No | 
| Maximum number of Edge\-Optimized APIs per account per region | 120 | No | 
| Maximum length, in characters, of API Gateway resource policy | 8092 | Yes | 
| Maximum number of API keys per account per region | 500 | Yes | 
| Maximum number of client certificates per account per region | 60 | Yes | 
| Maximum number of Lambda authorizers per API | 10 | Yes | 
| Maximum number of documentation parts per API | 2000 | Yes | 
| Maximum number of resources per API | 300 | Yes | 
| Maximum number of stages per API | 10 | Yes | 
| Maximum number of usage plans per account per region | 300 | Yes | 
| Maximum number of usage plans per API key | 10 | Yes | 
| Maximum number of per\-method throttling limit settings per API stage | 20 | Yes | 
| Maximum number of VPC links per account per region | 5 | Yes | 
| API caching TTL | 300 seconds by default and configurable between 0 and 3600 by an API owner\. | Not for the upper bound \(3600\) | 
| Integration timeout | 50 milliseconds \- 29 seconds for all integration types, including Lambda, Lambda proxy, HTTP, HTTP proxy, and AWS integrations\. | Not for the lower or upper bounds\. | 
| Header value size | 10240 Bytes | No | 
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
| [CreateRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-create/) | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html)[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html) | No | 
| [ImportRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html)[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html) | No | 
| [PutRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-put/) | 1 request per second per account | No | 
| [DeleteRestApi](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-delete/) | 1 request every 30 seconds per account | No | 
| [CreateDeployment](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/) | 1 request every 5 seconds per account | No | 
| [UpdateAccount](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/account-update/) | 1 request every 20 seconds per account | No | 
| [GetResources](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-resources/) | 5 requests every 2 seconds per account | No | 
| [CreateResource](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-create/) | 5 requests per second per account | No | 
| [DeleteResource](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-delete/) | 5 requests per second per account | No | 
| [CreateDomainName](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-create/) | 1 request every 30 seconds per account | No | 
| [UpdateUsagePlan](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/usageplan-update/) | 1 request every 20 seconds per account | No | 
| Other operations | No limit up to the total account limit\. | No | 
| Total operations | 10 requests per second with a burst limit of 40 requests per second\. | No | 