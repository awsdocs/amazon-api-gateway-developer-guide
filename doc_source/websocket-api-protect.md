# Protecting your WebSocket API<a name="websocket-api-protect"></a>

You can configure throttling and quotas for your APIs to help protect them from being overwhelmed by too many requests\. Both throttles and quotas are applied on a best\-effort basis and should be thought of as targets rather than guaranteed request ceilings\.

API Gateway throttles requests to your API using the token bucket algorithm, where a token counts for a request\. Specifically, API Gateway examines the rate and a burst of request submissions against all APIs in your account, per Region\. In the token bucket algorithm, a burst can allow pre\-defined overrun of those limits, but other factors can also cause limits to be overrun in some cases\.

When request submissions exceed the steady\-state request rate and burst limits, API Gateway begins to throttle requests\. Clients may receive `429 Too Many Requests` error responses at this point\. Upon catching such exceptions, the client can resubmit the failed requests in a way that is rate limiting\.

As an API developer, you can set the target limits for individual API stages or routes to improve overall performance across all APIs in your account\.

## Account\-level throttling per Region<a name="websocket-api-protect-throttling-account"></a>

By default, API Gateway limits the steady\-state requests per second \(RPS\) across all APIs within an AWS account, per Region\. It also limits the burst \(that is, the maximum bucket size\) across all APIs within an AWS account, per Region\. In API Gateway, the burst limit represents the target maximum number of concurrent request submissions that API Gateway will fulfill before returning `429 Too Many Requests` error responses\. For more information on throttling quotas, see [Amazon API Gateway quotas and important notes](limits.md)\.

Per\-account limits are applied to all APIs in an account in a specified Region\. The account\-level rate limit can be increased upon request \- higher limits are possible with APIs that have shorter timeouts and smaller payloads\. To request an increase of account\-level throttling limits per Region, contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\. For more information, see [Amazon API Gateway quotas and important notes](limits.md)\. Note that these limits can't be higher than the AWS throttling limits\.

## Route\-level throttling<a name="websocket-api-protect-throttling-route"></a>

You can set route\-level throttling to override the account\-level request throttling limits for a specific stage or for individual routes in your API\. The default route throttling limits can't exceed account\-level rate limits\.