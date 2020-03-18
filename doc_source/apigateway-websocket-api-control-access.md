# Controlling and Managing Access to a WebSocket API in API Gateway<a name="apigateway-websocket-api-control-access"></a>

API Gateway supports multiple mechanisms for controlling and managing access to your WebSocket API\.

You can use the following mechanisms for authentication and authorization:
+ **Standard AWS IAM roles and policies** offer flexible and robust access controls\. You can use IAM roles and policies for controlling who can create and manage your APIs, as well as who can invoke them\. For more information, see [Using IAM Authorization](apigateway-websocket-control-access-iam.md)\.
+ **IAM tags** can be used together with IAM policies to control access\. For more information, see [Using Tags to Control Access to API Gateway Resources](apigateway-tagging-iam-policy.md)\.
+ **Lambda authorizers** are Lambda functions that control access to APIs\. For more information, see [Creating a Lambda `REQUEST` Authorizer Function](apigateway-websocket-api-lambda-auth.md)\.

**Topics**
+ [Using IAM Authorization](apigateway-websocket-control-access-iam.md)
+ [Creating a Lambda `REQUEST` Authorizer Function](apigateway-websocket-api-lambda-auth.md)