# Create and Use Usage Plans with API Keys<a name="api-gateway-api-usage-plans"></a>

After you create, test, and deploy your APIs, you can use API Gateway usage plans to make them available as product offerings for your customers\. You can configure usage plans and API keys to allow customers to access selected APIs at agreed\-upon request rates and quotas that meet their business requirements and budget constraints\. If desired, you can set default method\-level throttling limits for an API or set throttling limits for individual API methods\.

## What Are Usage Plans and API Keys?<a name="api-gateway-api-usage-plans-overview"></a>

A *usage plan* specifies who can access one or more deployed API stages and methods — and also how much and how fast they can access them\. The plan uses API keys to identify API clients and meters access to the associated API stages for each key\. It also lets you configure throttling limits and quota limits that are enforced on individual client API keys\. A *throttling limit* is a request rate limit that is applied to each API key that you add to the usage plan\. You can also set a default method\-level throttling limit for an API or set throttling limits for individual API methods\. A *quota limit* is the maximum number of requests with a given API key that can be submitted within a specified time interval\. You can configure individual API methods to require API key authorization based on usage plan configuration\. An API stage is identified by an API identifier and a stage name\.

*API keys* are alphanumeric strings that you distribute to app developer customers to grant access to your API\. You can use API keys together with [usage plans](#api-gateway-api-usage-plans) or [Lambda authorizers](apigateway-use-lambda-authorizer.md) to control access to your APIs\. API Gateway can generate API keys on your behalf, or you can import them from a CSV file\.

An API key can be associated with more than one usage plan\. A usage plan can be associated with more than one stage\. However, a given API key can only be associated with one usage plan for each stage of your API\.

**Note**  
Throttling and quota limits apply to requests for individual API keys that are aggregated across all API stages within a usage plan\.

You can generate an API key in API Gateway, or import it into API Gateway from an external source\. For more information, see [Set Up API Keys Using the API Gateway Console](api-gateway-setup-api-key-with-console.md)\. 

## Steps to Configure a Usage Plan<a name="api-gateway-create-usage-plans"></a>

The following steps outline how you, as the API owner, create and configure a usage plan for your customers\.

**To configure a usage plan**

1. Create one or more APIs, configure the methods to require an API key, and deploy the APIs in stages\.

1. Generate or import API keys to distribute to app developers \(your customers\) who will be using your API\.

1. Create the usage plan with the desired throttle and quota limits\.

1. Associate API stages and API keys with the usage plan\.

Callers of the API must supply an assigned API key in the `x-api-key` header in requests to the API\.

**Note**  
To include API methods in a usage plan, you must configure individual API methods to [require an API key](api-gateway-setup-api-key-with-console.md)\. For user authentication and authorization, don't use API keys\. Use an IAM role, [a Lambda authorizer](apigateway-use-lambda-authorizer.md), or an [Amazon Cognito user pool](apigateway-integrate-with-cognito.md)\.