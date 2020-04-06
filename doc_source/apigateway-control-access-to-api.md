# Controlling and managing access to a REST API in API Gateway<a name="apigateway-control-access-to-api"></a>

API Gateway supports multiple mechanisms for controlling and managing access to your API\.

You can use the following mechanisms for authentication and authorization:
+ **Resource policies** let you create resource\-based policies to allow or deny access to your APIs and methods from specified source IP addresses or VPC endpoints\. For more information, see [Controlling access to an API with API Gateway resource policies](apigateway-resource-policies.md)\.
+ **Standard AWS IAM roles and policies** offer flexible and robust access controls that can be applied to an entire API or individual methods\. IAM roles and policies can be used for controlling who can create and manage your APIs, as well as who can invoke them\. For more information, see [Control access to an API with IAM permissions](permissions.md)\.
+ **IAM tags** can be used together with IAM policies to control access\. For more information, see [Using tags to control access to API Gateway resources](apigateway-tagging-iam-policy.md)\.
+ **Endpoint policies for interface VPC endpoints** allow you to attach IAM resource policies to interface VPC endpoints to improve the security of your [private APIs](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-private-apis.html)\. For more information, see [Use VPC endpoint policies for private APIs in API Gateway](apigateway-vpc-endpoint-policies.md)\.
+ **Lambda authorizers** are Lambda functions that control access to REST API methods using bearer token authentication—as well as information described by headers, paths, query strings, stage variables, or context variables request parameters\. Lambda authorizers are used to control who can invoke REST API methods\. For more information, see [Use API Gateway Lambda authorizers](apigateway-use-lambda-authorizer.md)\.
+ **Amazon Cognito user pools** let you create customizable authentication and authorization solutions for your REST APIs\. Amazon Cognito user pools are used to control who can invoke REST API methods\. For more information, see [Control access to a REST API using Amazon Cognito User Pools as authorizer](apigateway-integrate-with-cognito.md)\.

You can use the following mechanisms for performing other tasks related to access control:
+ **Cross\-origin resource sharing \(CORS\)** lets you control how your REST API responds to cross\-domain resource requests\. For more information, see [Enabling CORS for a REST API resource](how-to-cors.md)\.
+ **Client\-side SSL certificates** can be used to verify that HTTP requests to your backend system are from API Gateway\. For more information, see [Generate and configure an SSL certificate for backend authentication](getting-started-client-side-ssl-authentication.md)\.
+ **AWS WAF** can be used to protect your API Gateway API from common web exploits\. For more information, see [Using AWS WAF to protect your APIs](apigateway-control-access-aws-waf.md)\.

You can use the following mechanisms for tracking and limiting the access that you have granted to authorized clients:
+ **Usage plans** let you provide **API keys** to your customers—and then track and limit usage of your API stages and methods for each API key\. For more information, see [Creating and using usage plans with API keys](api-gateway-api-usage-plans.md)\.