# Throttle API Requests for Better Throughput<a name="api-gateway-request-throttling"></a>

 To prevent your API from being overwhelmed by too many requests, Amazon API Gateway throttles requests to your API using the [token bucket algorithm](https://en.wikipedia.org/wiki/Token_bucket), where a token counts for a request\. Specifically, API Gateway sets a limit on a steady\-state rate and a burst of request submissions against all APIs in your account\. In the token bucket algorithm, the burst is the maximum bucket size\. 

 When request submissions exceed the steady\-state request rate and burst limits, API Gateway fails the limit\-exceeding requests and returns `429 Too Many Requests` error responses to the client\. Upon catching such exceptions, the client can resubmit the failed requests in a rate\-limiting fashion, while complying with the API Gateway throttling limits\. 

 As an API developer, you can set the limits for individual API stages or methods to improve overall performance across all APIs in your account\. Alternatively, you can enable [usage plans](api-gateway-api-usage-plans.md) to restrict client request submissions to within specified request rates and quotas\. This restricts the overall request submissions so that they don't go significantly past the account\-level throttling limits\. 

## Account\-Level Throttling<a name="apig-request-throttling-account-level-limits"></a>

 By default, API Gateway limits the steady\-state request rate to 10,000 requests per second \(rps\)\. It limits the burst \(that is, the maximum bucket size\) to 5,000 requests across all APIs within an AWS account\. In API Gateway, the burst limit corresponds to the maximum number of concurrent request submissions that API Gateway can fulfill at any moment without returning `429 Too Many Requests` error responses\. 

To help understand these throttling limits, here are a few examples, given the burst limit and the default account\-level rate limit:
+ If a caller submits 10,000 requests in a one second period evenly \(for example, 10 requests every millisecond\), API Gateway processes all requests without dropping any\. 
+ If the caller sends 10,000 requests in the first millisecond, API Gateway serves 5,000 of those requests and throttles the rest in the one\-second period\.
+ If the caller submits 5,000 requests in the first millisecond and then evenly spreads another 5,000 requests through the remaining 999 milliseconds \(for example, about 5 requests every millisecond\), API Gateway processes all 10,000 requests in the one\-second period without returning `429 Too Many Requests` error responses\. 
+ If the caller submits 5,000 requests in the first millisecond and waits until the 101st millisecond to submit another 5,000 requests, API Gateway processes 6,000 requests and throttles the rest in the one\-second period\. This is because at the rate of 10,000 rps, API Gateway has served 1,000 requests after the first 100 milliseconds and thus emptied the bucket by the same amount\. Of the next spike of 5,000 requests, 1,000 fill the bucket and are queued to be processed\. The other 4,000 exceed the bucket capacity and are discarded\.
+ If the caller submits 5,000 requests in the first millisecond, submits 1,000 requests at the 101st millisecond, and then evenly spreads another 4,000 requests through the remaining 899 milliseconds, API Gateway processes all 10,000 requests in the one\-second period without throttling\.

 More generally, at any given moment, when a bucket contains `b` and the maximum bucket capacity is `B`, the maximum additional tokens that can be added to the bucket is `Δ=B-b`\. This maximum number of additional tokens corresponds to the maximum number of additional concurrent requests that a client can submit without receiving any `429` error responses\. In general, `Δ` varies in time\. The value ranges from zero when the bucket is full \(that is, `b=B`\) to `B` when the bucket is empty \(that is, `b=0`\)\. The range depends on the request\-processing rate, which is the rate at which tokens are removed from the bucket, and the rate limit rate, which is the rate at which tokens are added to the bucket\. 

 The following schematic shows the general behaviors of `Δ`, the maximum additional concurrent requests, as a function of time\. The schematic assumes that the tokens in the bucket decrease at a combined rate of `r`, starting from an empty bucket\. 

![\[Burst as a function of time given the token bucket size and a combined burn rate\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/tokenBucketBurst.png)

The account\-level rate limit can be increased upon request\. To request an increase of account\-level throttling limits, contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\. For more information, see [API Gateway Limits](limits.md#api-gateway-limits)\. 

**Note**  
The burst limit cannot be increased\.

## Default Method Throttling<a name="apig-request-throttling-stage-and-method-level-limits"></a>

 As an API owner, you can set the default method throttling to override the account\-level request throttling limits for a specific stage or for individual methods in an API\. The Default method throttling limits are bounded by the account\-level rate limits, even if you set the default method throttling limits higher than the account\-level limits\. 

 You can set the default method throttling limits by using the API Gateway console or by calling the [API Gateway REST API](api-ref.md)\. For instructions on using the console, see [Update Stage Settings](stages.md#how-to-stage-settings)\. 