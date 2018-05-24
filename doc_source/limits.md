# Amazon API Gateway Limits, Pricing and Known Issues<a name="limits"></a>

**Topics**
+ [API Gateway Limits](#api-gateway-limits)
+ [API Gateway Pricing](api-gateway-pricing.md)
+ [Known Issues](api-gateway-known-issues.md)

## API Gateway Limits<a name="api-gateway-limits"></a>

Unless noted otherwise, the limits can be increased upon request\. To request a limit increase, contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

 When authorization is enabled on a method, the maximum length of the method's ARN \(e\.g\., `arn:aws:execute-api:{region-id}:{account-id}:{api-id}/{stage-id}/{method}/{resource}/{path}`\) is 1600 bytes\. The path parameter values, the size of which are determined at run time, can cause the ARN length to exceed the limit\. When this happens, the API client will receive a `414 Request URI too long` response\. 

 Header values are limited to 10240 bytes\. 

### API Gateway Limits for Configuring and Running an API<a name="api-gateway-execution-service-limits-table"></a>

The following limits apply to configuring and running an API in Amazon API Gateway\.


| Resource or Operation | Default Limit | Can Be Increased | 
| --- | --- | --- | 
| Throttle limits per account per region | 10000 request per second \(rps\) with an additional burst capacity provided by the [token bucket algorithm](https://en.wikipedia.org/wiki/Token_bucket), using a maximum bucket capacity of 5000 requests\.  The burst limit cannot be increased\.  | Yes | 
| APIs \(or [RestApi](http://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/)s\) per account per region | 60 | Yes | 
| Maximum length, in characters, of resource policy | 8092 | Yes | 
| API keys per account per region | 500 | Yes | 
| Client certificates per account per region | 60 | Yes | 
| Custom authorizers per API | 10 | Yes | 
| Documentation parts per API | 2000 | Yes | 
| Resources per API  | 300 | Yes | 
| Stages per API | 10 | Yes | 
| Usage plans per account per region | 300 | Yes | 
| Usage plans per API key | 10 | Yes | 
| VPC links per account per region | 5 | Yes | 
| API caching TTL | 300 seconds by default and configurable between 0 and 3600 by an API owner\. | Not for the upper bound \(3600\) | 
| Integration timeout | 50 milliseconds \- 29 seconds for all integration types, including Lambda, Lambda proxy, HTTP, HTTP proxy, and AWS integrations\. | Not for the lower or upper bounds\. | 
| Header value size | 10240 Bytes | No | 
| Payload size | 10 MB | No | 
| Tags per stage | 50 | No | 
| Number of iterations in a \#foreach \.\.\. \#end loop in mapping templates | 1000 | No | 
| ARN length of a method with authorization | 1600 bytes | No | 

For [restapi:import](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) or [restapi:put](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-put/), the maximum size of the API definition file is 2MB\. 

All of the per API limits can only be increased on specific APIs\.

### API Gateway Limits for Creating, Deploying and Managing an API<a name="api-gateway-control-service-limits-table"></a>

The following fixed limits apply to creating, deploying, and managing an API in API Gateway, using the AWS CLI, the API Gateway console, or the API Gateway REST API and its SDKs\. These limits cannot be increased\.


| Action | Default Limit | Can Be Increased | 
| --- | --- | --- | 
| [CreateRestApi](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-create/) | 2 requests per minute \(rpm\) per account\. | No | 
| [ImportRestApi](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) | 2 requests per minute per account | No | 
| [PutRestApi](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-put/) | 60 requests per minute per account | No | 
| [DeleteRestApi](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-delete/) | 2 requests per minute per account | No | 
| [CreateDeployment](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/) | 3 requests per minute per account | No | 
| [UpdateAccount](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/account-update/) | 3 requests per minute per account | No | 
| [GetResources](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-resources/) | 150 requests per minute per account | No | 
| [CreateResource](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-create/) | 300 requests per minute per account | No | 
| [DeleteResource](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-delete/) | 300 requests per minute per account | No | 
| [CreateDomainName](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-create/) | 2 requests per minute per account | No | 
| [UpdateUsagePlan](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/usageplan-update/) | 3 requests per minute per account | No | 
| Other operations | No limit up to the total account limit\. | No | 
| Total operations | 10 request per second \(rps\) with a burst limit of 40 requests\. | No | 