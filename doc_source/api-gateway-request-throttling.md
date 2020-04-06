# Throttle API requests for better throughput<a name="api-gateway-request-throttling"></a>

 To prevent your API from being overwhelmed by too many requests, Amazon API Gateway throttles requests to your API using the [token bucket algorithm](https://en.wikipedia.org/wiki/Token_bucket), where a token counts for a request\. Specifically, API Gateway sets a limit on a steady\-state rate and a burst of request submissions against all APIs in your account\. In the token bucket algorithm, the burst is the maximum bucket size\. 

 When request submissions exceed the steady\-state request rate and burst limits, API Gateway fails the limit\-exceeding requests and returns `429 Too Many Requests` error responses to the client\. Upon catching such exceptions, the client can resubmit the failed requests in a way that is rate limiting, while complying with the API Gateway throttling limits\. 

 As an API developer, you can set the limits for individual API stages or methods to improve overall performance across all APIs in your account\. Alternatively, you can enable [usage plans](api-gateway-api-usage-plans.md) to restrict client request submissions to within specified request rates and quotas\. This restricts the overall request submissions so that they don't go significantly past the account\-level throttling limits\. 

**Topics**
+ [How throttling limit settings are applied in API Gateway](#apigateway-how-throttling-limits-are-applied)
+ [Account\-level throttling](#apig-request-throttling-account-level-limits)
+ [Default method throttling and overriding default method throttling](#apig-request-throttling-stage-and-method-level-limits)
+ [Configuring API\-level and stage\-level throttling in a usage plan](#apigateway-api-level-throttling-in-usage-plan)
+ [Configuring method\-level throttling in a usage plan](#apigateway-method-level-throttling-in-usage-plan)

## How throttling limit settings are applied in API Gateway<a name="apigateway-how-throttling-limits-are-applied"></a>

Before you configure limit settings for your API in your stage settings and optionally a [usage plan](api-gateway-api-usage-plans.md), it's useful to understand Amazon API Gateway how throttling limit settings are applied\.

Amazon API Gateway provides two basic types of throttling\-related settings:
+ *Server\-side throttling limits* are applied across all clients\. These limit settings exist to prevent your API—and your account—from being overwhelmed by too many requests\.
+ *Per\-client throttling limits* are applied to clients that use API keys associated with your usage policy as client identifier\.

API Gateway throttling\-related settings are applied in the following order:

1. [Per\-client per\-method throttling limits](#apigateway-method-level-throttling-in-usage-plan) that you set for an API stage in a [usage plan](api-gateway-create-usage-plans-with-console.md#api-gateway-usage-plan-create)

1. [Per\-client throttling limits](#apigateway-api-level-throttling-in-usage-plan) that you set in a usage plan

1. [Default per\-method limits and individual per\-method limits](#apig-request-throttling-stage-and-method-level-limits) that you set in [API stage settings](stages.md#how-to-stage-settings)

1. [Account\-level throttling](#apig-request-throttling-account-level-limits)

## Account\-level throttling<a name="apig-request-throttling-account-level-limits"></a>

 By default, API Gateway limits the steady\-state request rate to 10,000 requests per second \(rps\)\. It limits the burst \(that is, the maximum bucket size\) to 5,000 requests across all APIs within an AWS account\. In API Gateway, the burst limit corresponds to the maximum number of concurrent request submissions that API Gateway can fulfill at any moment without returning `429 Too Many Requests` error responses\. 

To help understand these throttling limits, here are a few examples, given the burst limit and the default account\-level rate limit:
+ If a caller submits 10,000 requests in a one\-second period evenly \(for example, 10 requests every millisecond\), API Gateway processes all requests without dropping any\. 
+ If the caller sends 10,000 requests in the first millisecond, API Gateway serves 5,000 of those requests and throttles the rest in the one\-second period\.
+ If the caller submits 5,000 requests in the first millisecond and then evenly spreads another 5,000 requests through the remaining 999 milliseconds \(for example, about 5 requests every millisecond\), API Gateway processes all 10,000 requests in the one\-second period without returning `429 Too Many Requests` error responses\. 
+ If the caller submits 5,000 requests in the first millisecond and waits until the 101st millisecond to submit another 5,000 requests, API Gateway processes 6,000 requests and throttles the rest in the one\-second period\. This is because at the rate of 10,000 rps, API Gateway has served 1,000 requests after the first 100 milliseconds and thus emptied the bucket by the same amount\. Of the next spike of 5,000 requests, 1,000 fill the bucket and are queued to be processed\. The other 4,000 exceed the bucket capacity and are discarded\.
+ If the caller submits 5,000 requests in the first millisecond, submits 1,000 requests at the 101st millisecond, and then evenly spreads another 4,000 requests through the remaining 899 milliseconds, API Gateway processes all 10,000 requests in the one\-second period without throttling\.

 More generally, at any given moment, when a bucket contains `b` and the maximum bucket capacity is `B`, the maximum additional tokens that can be added to the bucket is `Δ=B-b`\. This maximum number of additional tokens corresponds to the maximum number of additional concurrent requests that a client can submit without receiving any `429` error responses\. In general, `Δ` varies in time\. The value ranges from zero when the bucket is full \(that is, `b=B`\) to `B` when the bucket is empty \(that is, `b=0`\)\. The range depends on the request\-processing rate \(the rate at which tokens are removed from the bucket\) and the rate limit rate \(the rate at which tokens are added to the bucket\)\. 

 The following schematic shows the general behaviors of `Δ`, the maximum additional concurrent requests, as a function of time\. The schematic assumes that the tokens in the bucket decrease at a combined rate of `r`, starting from an empty bucket\. 

![\[Burst as a function of time given the token bucket size and a combined burn rate\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/tokenBucketBurst.png)

The account\-level rate limit can be increased upon request\. To request an increase of account\-level throttling limits, contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\. For more information, see [API Gateway quotas](limits.md#api-gateway-limits)\. 

## Default method throttling and overriding default method throttling<a name="apig-request-throttling-stage-and-method-level-limits"></a>

You can set the default method throttling to override the account\-level request throttling limits for a specific stage or for individual methods in your API\. The default method throttling limits are bounded by the account\-level rate limits, even if you set the default method throttling limits higher than the account\-level limits\. 

You can set the default method throttling limits in the API Gateway console by using the **Default Method Throttling** setting in **Stages**\. For instructions on using the console, see [Update stage settings](stages.md#how-to-stage-settings)\.

You can also set the default method throttling limits by calling the [API references](api-ref.md)\.

## Configuring API\-level and stage\-level throttling in a usage plan<a name="apigateway-api-level-throttling-in-usage-plan"></a>

In a [usage plan](api-gateway-api-usage-plans.md), you can set a default per\-method throttling limit for all methods at the API or stage level under **Create Usage Plan** as shown in [Create a usage plan](api-gateway-create-usage-plans-with-console.md#api-gateway-usage-plan-create)\.

## Configuring method\-level throttling in a usage plan<a name="apigateway-method-level-throttling-in-usage-plan"></a>

You can set additional throttling limits at the method level in **Usage Plans** as shown in [Create a usage plan](api-gateway-create-usage-plans-with-console.md#api-gateway-usage-plan-create)\. In the API Gateway console, these are set by specifying `Resource=<resource>`, `Method=<method>` in the **Configure Method Throttling** setting\. For example, for the [PetStore example](api-gateway-create-api-step-by-step.md), you might specify `Resource=/pets`, `Method=GET`\.