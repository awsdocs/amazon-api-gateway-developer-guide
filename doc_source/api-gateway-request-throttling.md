# Throttle API requests for better throughput<a name="api-gateway-request-throttling"></a>

You can configure throttling and quotas for your APIs to help protect them from being overwhelmed by too many requests\. Both throttles and quotas are applied on a best\-effort basis and should be thought of as targets rather than guaranteed request ceilings\.

API Gateway throttles requests to your API using the token bucket algorithm, where a token counts for a request\. Specifically, API Gateway examines the rate and a burst of request submissions against all APIs in your account, per Region\. In the token bucket algorithm, a burst can allow pre\-defined overrun of those limits, but other factors can also cause limits to be overrun in some cases\.

When request submissions exceed the steady\-state request rate and burst limits, API Gateway begins to throttle requests\. Clients may receive `429 Too Many Requests` error responses at this point\. Upon catching such exceptions, the client can resubmit the failed requests in a way that is rate limiting\.

As an API developer, you can set the target limits for individual API stages or methods to improve overall performance across all APIs in your account\. Alternatively, you can enable usage plans to set throttles on client request submissions based on specified requests rates and quotas\.

**Topics**
+ [How throttling limit settings are applied in API Gateway](#apigateway-how-throttling-limits-are-applied)
+ [Account\-level throttling per Region](#apig-request-throttling-account-level-limits)
+ [Configuring API\-level and stage\-level throttling targets in a usage plan](#apigateway-api-level-throttling-in-usage-plan)
+ [Configuring method\-level throttling targets in a usage plan](#apigateway-method-level-throttling-in-usage-plan)

## How throttling limit settings are applied in API Gateway<a name="apigateway-how-throttling-limits-are-applied"></a>

Before you configure throttle and quota settings for your API, it's useful to understand how they are applied by Amazon API Gateway\.

Amazon API Gateway provides four basic types of throttling\-related settings:
+ *AWS throttling limits* are applied across all accounts and clients in a region\. These limit settings exist to prevent your API—and your account—from being overwhelmed by too many requests\. These limits are set by AWS and can't be changed by a customer\.
+ Per\-account limits are applied to all APIs in an account in a specified Region\. The account\-level rate limit can be increased upon request \- higher limits are possible with APIs that have shorter timeouts and smaller payloads\. To request an increase of account\-level throttling limits per Region, contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\. For more information, see [Amazon API Gateway quotas and important notes](limits.md)\. Note that these limits can't be higher than the AWS throttling limits\.
+ Per\-API, per\-stage throttling limits are applied at the API method level for a stage\. You can configure the same settings for all methods, or configure different throttle settings for each method\. Note that these limits can't be higher than the AWS throttling limits\.
+ *Per\-client throttling limits* are applied to clients that use API keys associated with your usage plan as client identifier\. Note that these limits can't be higher than the per\-account limits\.

API Gateway throttling\-related settings are applied in the following order:

1. [Per\-client or per\-method throttling limits](#apigateway-method-level-throttling-in-usage-plan) that you set for an API stage in a [usage plan](api-gateway-create-usage-plans-with-console.md#api-gateway-usage-plan-create)

1. Per\-method throttling limits that you set for an API stage\.

1. [Account\-level throttling per Region](#apig-request-throttling-account-level-limits)

1. AWS Regional throttling

## Account\-level throttling per Region<a name="apig-request-throttling-account-level-limits"></a>

By default, API Gateway limits the steady\-state requests per second \(RPS\) across all APIs within an AWS account, per Region\. It also limits the burst \(that is, the maximum bucket size\) across all APIs within an AWS account, per Region\. In API Gateway, the burst limit represents the target maximum number of concurrent request submissions that API Gateway will fulfill before returning `429 Too Many Requests` error responses\. For more information on throttling quotas, see [Amazon API Gateway quotas and important notes](limits.md)\.

## Configuring API\-level and stage\-level throttling targets in a usage plan<a name="apigateway-api-level-throttling-in-usage-plan"></a>

In a [usage plan](api-gateway-api-usage-plans.md), you can set a per\-method throttling target for all methods at the API or stage level under **Create Usage Plan**\.

## Configuring method\-level throttling targets in a usage plan<a name="apigateway-method-level-throttling-in-usage-plan"></a>

You can set additional throttling targets at the method level in **Usage Plans** as shown in [Create a usage plan](api-gateway-create-usage-plans-with-console.md#api-gateway-usage-plan-create)\. In the API Gateway console, these are set by specifying `Resource=<resource>`, `Method=<method>` in the **Configure Method Throttling** setting\. For example, for the [PetStore example](api-gateway-create-api-step-by-step.md), you might specify `Resource=/pets`, `Method=GET`\.