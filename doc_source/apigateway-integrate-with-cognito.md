# Use Amazon Cognito User Pools<a name="apigateway-integrate-with-cognito"></a>

As an alternative to using [IAM roles and policies](permissions.md) or [Lambda authorizers](apigateway-use-lambda-authorizer.md) \(formerly known as custom authorizers\), you can use an [Amazon Cognito user pool](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html) to control who can access your API in Amazon API Gateway\. 

To use an Amazon Cognito user pool with your API, you must first create an authorizer of the `COGNITO_USER_POOLS` type and then configure an API method to use that authorizer\. After the API is deployed, the client must first sign the user in to the user pool, obtain an [identity or access token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) for the user, and then call the API method with one of the tokens, which are typically set to the request's `Authorization` header\. The API call succeeds only if the required token is supplied and the supplied token is valid, otherwise, the client isn't authorized to make the call because the client did not have credentials that could be authorized\. 

The identity token is used to authorize API calls based on identity claims of the signed\-in user\. The access token is used to authorize API calls based on the custom scopes of specified access\-protected resources\. For more information, see [Using Tokens with User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) and [Resource Server and Custom Scopes](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-define-resource-servers.html)\.

To create and configure an Amazon Cognito user pool for your API, you perform the following tasks:
+ Use the Amazon Cognito console, CLI/SDK, or API to create a user pool—or use one that's owned by another AWS account\.
+ Use the API Gateway console, CLI/SDK, or API to create an API Gateway authorizer with the chosen user pool\.
+ Use the API Gateway console, CLI/SDK, or API to enable the authorizer on selected API methods\.

 To call any API methods with a user pool enabled, your API clients perform the following tasks:
+  Use the Amazon Cognito CLI/[SDK](https://github.com/aws/amazon-cognito-identity-js/) or API to sign a user in to the chosen user pool, and obtain an identity token or access token\.
+  Use a client\-specific framework to call the deployed API Gateway API and supply the appropriate token in the `Authorization` header\.

As the API developer, you must provide your client developers with the user pool ID, a client ID, and possibly the associated client secrets that are defined as part of the user pool\. 

**Note**  
To let a user sign in using Amazon Cognito credentials and also obtain temporary credentials to use with the permissions of an IAM role, use [Amazon Cognito Federated Identities](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html)\. Set the authorization type of your API to `AWS_IAM`\. 

In this section, we describe how to create a user pool, how to integrate an API Gateway API with the user pool, and how to invoke an API that's integrated with the user pool\. 

**Topics**
+ [Obtain Permissions to Create User Pool Authorizers](apigateway-user-pool-authorizer-permissions.md)
+ [Create an Amazon Cognito User Pool](apigateway-create-cognito-user-pool.md)
+ [Integrate an API with a User Pool](apigateway-enable-cognito-user-pool.md)
+ [Call an API Integrated with a User Pool](apigateway-invoke-api-integrated-with-cognito-user-pool.md)