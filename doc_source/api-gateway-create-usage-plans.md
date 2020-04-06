# Steps to configure a usage plan<a name="api-gateway-create-usage-plans"></a>

The following steps outline how you, as the API owner, create and configure a usage plan for your customers\.

**To configure a usage plan**

1. Create one or more APIs, configure the methods to require an API key, and deploy the APIs to stages\.

1. Generate or import API keys to distribute to application developers \(your customers\) who will be using your API\.

1. Create the usage plan with the desired throttle and quota limits\.

1. Associate API stages and API keys with the usage plan\.

Callers of the API must supply an assigned API key in the `x-api-key` header in requests to the API\.

**Note**  
To include API methods in a usage plan, you must configure individual API methods to [require an API key](api-gateway-setup-api-key-with-console.md)\. For user authentication and authorization, don't use API keys\. Use an IAM role, [a Lambda authorizer](apigateway-use-lambda-authorizer.md), or an [Amazon Cognito user pool](apigateway-integrate-with-cognito.md)\.