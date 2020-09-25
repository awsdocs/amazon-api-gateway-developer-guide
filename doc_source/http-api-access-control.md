# Controlling and managing access to an HTTP API in API Gateway<a name="http-api-access-control"></a>

API Gateway supports multiple mechanisms for controlling and managing access to your HTTP API:
+ **Lambda authorizers** use Lambda functions to control access to APIs\. For more information, see [Working with AWS Lambda authorizers for HTTP APIs](http-api-lambda-authorizer.md)\.
+ **JWT authorizers** use JSON web tokens to control access to APIs\. For more information, see [Controlling access to HTTP APIs with JWT authorizers](http-api-jwt-authorizer.md)\.
+ **Standard AWS IAM roles and policies** offer flexible and robust access controls\. You can use IAM roles and policies to control who can create and manage your APIs, as well as who can invoke them\. For more information, see [Using IAM authorization](http-api-access-control-iam.md)\.