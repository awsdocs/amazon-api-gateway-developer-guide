# Create an Amazon Cognito user pool for a REST API<a name="apigateway-create-cognito-user-pool"></a>

Before integrating your API with a user pool, you must create the user pool in Amazon Cognito\. For instructions on how to create a user pool, see [Setting up User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/setting-up-cognito-user-identity-pools.html) in the *Amazon Cognito Developer Guide*\.

**Note**  
Note the user pool ID, client ID, and any client secret\. The client must provide them to Amazon Cognito for the user to register with the user pool, to sign in to the user pool, and to obtain an identity or access token to be included in requests to call API methods that are configured with the user pool\. Also, you must specify the user pool name when you configure the user pool as an authorizer in API Gateway, as described next\.

If you're using access tokens to authorize API method calls, be sure to configure the app integration with the user pool to set up the custom scopes that you want on a given resource server\. For more information about using tokens with Amazon Cognito user pools, see [Using Tokens with User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html)\. For more information about resource servers, see [Defining Resource Servers for Your User Pool](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-define-resource-servers.html)\.

Note the configured resource server identifiers and custom scope names\. You need them to construct the access scope full names for **OAuth Scopes**, which is used by the `COGNITO_USER_POOLS` authorizer\. 

![\[Amazon Cognito user pool resource servers and scopes\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/cognito-user-pool-custom-scopes.png)