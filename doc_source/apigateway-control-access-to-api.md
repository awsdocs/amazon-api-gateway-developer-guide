# Controlling Access to an API in API Gateway<a name="apigateway-control-access-to-api"></a>

API Gateway supports multiple mechanisms for controlling access to your API:
+ **Resource policies** let you create resource\-based policies to allow or deny access to your APIs and methods from specified source IP addresses or VPC endpoints\.
+ **Standard AWS IAM roles and policies** offer flexible and robust access controls that can be applied to an entire API or individual methods\.
+ **Cross\-origin resource sharing \(CORS\)** lets you control how your REST API responds to cross\-domain resource requests\.
+ **Lambda authorizers** are Lambda functions that control access to REST API methods using bearer token authentication as well as information described by headers, paths, query strings, stage variables, or context variables request parameters\.
+ **Amazon Cognito user pools** let you create customizable authentication and authorization solutions for your REST APIs\.
+ **Client\-side SSL certificates** can be used to verify that HTTP requests to your backend system are from API Gateway\.
+ **Usage plans** let you provide API keys to your customers — and then track and limit usage of your API stages and methods for each API key\.

**Topics**
+ [Control Access to an API with Amazon API Gateway Resource Policies](apigateway-resource-policies.md)
+ [Control Access to an API with IAM Permissions](permissions.md)
+ [Use API Gateway Lambda Authorizers](apigateway-use-lambda-authorizer.md)
+ [Control Access to a REST API Using Amazon Cognito User Pools as Authorizer](apigateway-integrate-with-cognito.md)
+ [Enable CORS for an API Gateway REST API Resource](how-to-cors.md)
+ [Use Client\-Side SSL Certificates for Authentication by the Backend](getting-started-client-side-ssl-authentication.md)
+ [Create and Use Usage Plans with API Keys](api-gateway-api-usage-plans.md)