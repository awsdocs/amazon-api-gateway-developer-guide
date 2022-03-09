# Creating and using usage plans with API keys<a name="api-gateway-api-usage-plans"></a>

After you create, test, and deploy your APIs, you can use API Gateway usage plans to make them available as product offerings for your customers\. You can configure usage plans and API keys to allow customers to access selected APIs, and begin throttling requests to those APIs based on defined limits and quotas\. These can be set at the API, or API method level\.

## What are usage plans and API keys?<a name="api-gateway-api-usage-plans-overview"></a>

A *usage plan* specifies who can access one or more deployed API stages and methods—and optionally sets a throttling and quota limit on each API key\. Throttling limits define the maximum number of requests per second available to each key\. Quota limits define the number of requests each API key is allowed to make over a period\. The plan uses API keys to identify API clients and who can access the associated API stages for each key.

*API keys* are alphanumeric string values that you distribute to application developer customers to grant access to your API\. You can use API keys together with [Lambda authorizers](apigateway-use-lambda-authorizer.md), [IAM roles](permissions.md), or [Amazon Cognito](apigateway-integrate-with-cognito.md) to control access to your APIs\. API Gateway can generate API keys on your behalf, or you can import them from a [CSV file](api-key-file-format.md)\. You can generate an API key in API Gateway, or import it into API Gateway from an external source\. For more information, see [Set up API keys using the API Gateway console](api-gateway-setup-api-key-with-console.md)\. 

An API key has a name and a value\. \(The terms "API key" and "API key value" are often used interchangeably\.\) The value is an alphanumeric string between 30 and 128 characters, for example, `apikey1234abcdefghij0123456789`\.

**Important**  
API key values must be unique\. If you try to create two API keys with different names and the same value, API Gateway considers them to be the same API key\.  
An API key can be associated with more than one usage plan\. A usage plan can be associated with more than one stage\. However, a given API key can only be associated with one usage plan for each stage of your API\.

A *throttling limit* sets the target point at which request throttling should start\. This can be set at the API or API method level\.

A *quota limit* sets the target maximum number of requests with a given API key that can be submitted within a specified time interval\. You can configure individual API methods to require API key authorization based on usage plan configuration\.

Throttling and quota limits apply to requests for individual API keys that are aggregated across all API stages within a usage plan\.

**Note**  
Usage plan throttling and quotas are not hard limits, and are applied on a best\-effort basis\. In some cases, clients can exceed the quotas that you set\. Don’t rely on usage plan quotas or throttling to control costs or block access to an API\. Consider using [AWS Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) to monitor costs and [AWS WAF](https://docs.aws.amazon.com/waf/latest/developerguide/waf-chapter.html) to manage API requests\.

## Best practices for API keys and usage plans<a name="apigateway-usage-plans-best-practices"></a>

The following are suggested best practices to follow when using API keys and usage plans\.
+ Don't rely on API keys as your only means of authentication and authorization for your APIs\. If you have multiple APIs in a usage plan, a user with a valid API key for one API in that usage plan can access *all* APIs in that usage plan\. Instead, use an IAM role, [a Lambda authorizer](apigateway-use-lambda-authorizer.md), or an [Amazon Cognito user pool](apigateway-integrate-with-cognito.md)\.
+ If you're using a [developer portal](apigateway-developer-portal.md) to publish your APIs, note that all APIs in a given usage plan are subscribable, even if you haven't made them visible to your customers\.
+ In some cases, clients can exceed the quotas that you set\. Don’t rely on usage plans to control costs\. Consider using [AWS Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) to monitor costs and [AWS WAF](https://docs.aws.amazon.com/waf/latest/developerguide/waf-chapter.html) to manage API requests\.