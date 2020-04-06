# Creating and using usage plans with API keys<a name="api-gateway-api-usage-plans"></a>

After you create, test, and deploy your APIs, you can use API Gateway usage plans to make them available as product offerings for your customers\. You can configure usage plans and API keys to allow customers to access selected APIs at agreed\-upon request rates and quotas that meet their business requirements and budget constraints\. If desired, you can set default method\-level throttling limits for an API or set throttling limits for individual API methods\.

## What are usage plans and API keys?<a name="api-gateway-api-usage-plans-overview"></a>

A *usage plan* specifies who can access one or more deployed API stages and methods—and also how much and how fast they can access them\. The plan uses API keys to identify API clients and meters access to the associated API stages for each key\. It also lets you configure throttling limits and quota limits that are enforced on individual client API keys\.

*API keys* are alphanumeric string values that you distribute to application developer customers to grant access to your API\. You can use API keys together with [usage plans](#api-gateway-api-usage-plans) or [Lambda authorizers](apigateway-use-lambda-authorizer.md) to control access to your APIs\. API Gateway can generate API keys on your behalf, or you can import them from a [CSV file](api-key-file-format.md)\. You can generate an API key in API Gateway, or import it into API Gateway from an external source\. For more information, see [Set up API keys using the API Gateway console](api-gateway-setup-api-key-with-console.md)\. 

An API key has a name and a value\. \(The terms "API key" and "API key value" are often used interchangeably\.\) The value is an alphanumeric string between 30 and 128 characters, for example, `apikey1234abcdefghij0123456789`\.

**Important**  
API key values must be unique\. If you try to create two API keys with different names and the same value, API Gateway considers them to be the same API key\.  
An API key can be associated with more than one usage plan\. A usage plan can be associated with more than one stage\. However, a given API key can only be associated with one usage plan for each stage of your API\.

A *throttling limit* is a request rate limit that is applied to each API key that you add to the usage plan\. You can also set a default method\-level throttling limit for an API or set throttling limits for individual API methods\.

A *quota limit* is the maximum number of requests with a given API key that can be submitted within a specified time interval\. You can configure individual API methods to require API key authorization based on usage plan configuration\. You can also use the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/get-usage.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/get-usage.html) CLI command or the [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/usage-get/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/usage-get/) REST API method to determine the usage for an API customer\.

**Note**  
Throttling and quota limits apply to requests for individual API keys that are aggregated across all API stages within a usage plan\.

## Best practices for API keys and usage plans<a name="apigateway-usage-plans-best-practices"></a>

The following are suggested best practices to follow when using API keys and usage plans\.
+ Don't rely on API keys as your only means of authentication and authorization for your APIs\. For one thing, if you have multiple APIs in a usage plan, a user with a valid API key for one API in that usage plan can access *all* APIs in that usage plan\. Instead, use an IAM role, [a Lambda authorizer](apigateway-use-lambda-authorizer.md), or an [Amazon Cognito user pool](apigateway-integrate-with-cognito.md)\.
+ If you're using a [developer portal](apigateway-developer-portal.md) to publish your APIs, note that all APIs in a given usage plan are subscribable, even if you haven't made them visible to your customers\.