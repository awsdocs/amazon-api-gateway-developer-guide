# Integrate a REST API with an Amazon Cognito user pool<a name="apigateway-enable-cognito-user-pool"></a>

After creating an Amazon Cognito user pool, in API Gateway, you must then create a `COGNITO_USER_POOLS` authorizer that uses the user pool\. The following procedure walks you through the steps to do this using the API Gateway console\.

**Important**  
After performing any of the procedures below, you'll need to deploy or redeploy your API to propagate the changes\. For more information about deploying your API, see [Deploying a REST API in Amazon API Gateway](how-to-deploy-api.md)\.

**To create a `COGNITO_USER_POOLS` authorizer by using the API Gateway console**

1. Create a new API, or select an existing API in API Gateway\.

1. From the main navigation pane, choose **Authorizers** under the specified API\.

1. Under **Authorizers**, choose **Create New Authorizer**\. 

1. To configure the new authorizer to use a user pool, do the following:

   1. Type an authorizer name in **Name**\.

   1. Select the **Cognito** option\.

   1. Choose a region under **Cognito User Pool**\.

   1. Select an available user pool\. You must have created a user pool for the selected region in Amazon Cognito for it to show up in the drop\-down list\.

   1.  For **Token source**, type `Authorization` as the header name to pass the identity or access token that's returned by Amazon Cognito when a user signs in successfully\. 

   1. Optionally, enter a regular expression in the **Token validation** field to validate the `aud` \(audience\) field of the identity token before the request is authorized with Amazon Cognito\. Note that when using an access token this validation rejects the request due to the access token not containing the `aud` field\.

   1. To finish integrating the user pool with the API, choose **Create**\. 

1. After creating the `COGNITO_USER_POOLS` authorizer, you can optionally test invoke it by supplying an identity token that's provisioned from the user pool\. You can obtain this identity token by calling the [Amazon Cognito Identity SDK](https://docs.aws.amazon.com/cognito/latest/developerguide/setting-up-the-javascript-sdk.html) to perform user sign\-in\. Make sure to use the returned identity token, not the access token\. 

The preceding procedure creates a `COGNITO_USER_POOLS` authorizer that uses the newly created Amazon Cognito user pool\. Depending on how you enable the authorizer on an API method, you can use either an identity token or an access token that's provisioned from the integrated user pool\. The next procedure walks you through the steps to configure the authorizer on an API method\.

**To configure a `COGNITO_USER_POOLS` authorizer on methods**

1. Choose \(or create\) a method on your API\. 

1. Choose **Method Request**\.

1. Under **Settings**, choose the pencil icon next to **Authorization**\.

1. Choose one of the available **Amazon Cognito user pool authorizers** from the drop\-down list\.

1. To save the settings, choose the check mark icon\.

1.  To use an identity token, do the following:

   1. Leave the **OAuth Scopes** option unspecified \(as `NONE`\)\.

   1. If needed, choose **Integration Request** to add the `$context.authorizer.claims['property-name']` or `$context.authorizer.claims.property-name` expressions in a body\-mapping template to pass the specified identity claims property from the user pool to the backend\. For simple property names, such as `sub` or `custom-sub`, the two notations are identical\. For complex property names, such as `custom:role`, you can't use the dot notation\. For example, the following mapping expressions pass the claim's [standard fields](http://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) of `sub` and `email` to the backend:

      ```
      {
      	"context" : {
      		"sub" : "$context.authorizer.claims.sub",
      		"email" : "$context.authorizer.claims.email"
      	}
      }
      ```

      If you declared a custom claim field when you configured a user pool, you can follow the same pattern to access the custom fields\. The following example gets a custom `role` field of a claim:

      ```
      {
      	"context" : {
      		"role" : "$context.authorizer.claims.role"
          }
      }
      ```

      If the custom claim field is declared as `custom:role`, use the following example to get the claim's property:

      ```
      {
      	"context" : {
      		"role" : "$context.authorizer.claims['custom:role']"
          }
      }
      ```

1.  To use an access token, do the following: 

   1. Choose the pencil icon next to **OAuth Scopes**\. 

   1. Type one or more full names of a scope that has been configured when the Amazon Cognito user pool was created\. For example, following the example given in [Create an Amazon Cognito user pool for a REST API](apigateway-create-cognito-user-pool.md), one of the scopes is `com.hamuta.movies/drama.view`\. Use a single space to separate multiple scopes\. 

      At runtime, the method call succeeds if any scope that's specified on the method in this step matches a scope that's claimed in the incoming token\. Otherwise, the call fails with a `401 Unauthorized` response\.

   1.  To save the setting, choose the check mark icon\.

1. Repeat these steps for other methods that you choose\.

With the `COGNITO_USER_POOLS` authorizer, if the **OAuth Scopes** option isn't specified, API Gateway treats the supplied token as an identity token and verifies the claimed identity against the one from the user pool\. Otherwise, API Gateway treats the supplied token as an access token and verifies the access scopes that are claimed in the token against the authorization scopes declared on the method\.

Instead of using the API Gateway console, you can also enable an Amazon Cognito user pool on a method by specifying an OpenAPI definition file and importing the API definition into API Gateway\.

**To import a COGNITO\_USER\_POOLS authorizer with an OpenAPI definition file**

1. Create \(or export\) an OpenAPI definition file for your API\.

1. Specify the `COGNITO_USER_POOLS` authorizer \(`MyUserPool`\) JSON definition as part of the `securitySchemes` section in OpenAPI 3\.0 or the `securityDefinitions` section in Open API 2\.0 as follows:

------
#### [ OpenAPI 3\.0 ]

   ```
     "securitySchemes": {
       "MyUserPool": {
         "type": "apiKey",
         "name": "Authorization",
         "in": "header",
         "x-amazon-apigateway-authtype": "cognito_user_pools",
         "x-amazon-apigateway-authorizer": {
           "type": "cognito_user_pools",
           "providerARNs": [
             "arn:aws:cognito-idp:{region}:{account_id}:userpool/{user_pool_id}"
           ]
         }
       }
   ```

------
#### [ OpenAPI 2\.0 ]

   ```
     "securityDefinitions": {
       "MyUserPool": {
         "type": "apiKey",
         "name": "Authorization",
         "in": "header",
         "x-amazon-apigateway-authtype": "cognito_user_pools",
         "x-amazon-apigateway-authorizer": {
           "type": "cognito_user_pools",
           "providerARNs": [
             "arn:aws:cognito-idp:{region}:{account_id}:userpool/{user_pool_id}"
           ]
         }
       }
   ```

------

1. To use the identity token for method authorization, add `{ "MyUserPool": [] }` to the `security` definition of the method, as shown in the following GET method on the root resource\.

   ```
     "paths": {
       "/": {
         "get": {
           "consumes": [
             "application/json"
           ],
           "produces": [
             "text/html"
           ],
           "responses": {
             "200": {
               "description": "200 response",
               "headers": {
                 "Content-Type": {
                   "type": "string"
                 }
               }
             }
           },
           "security": [
             {
               "MyUserPool": []
             }
           ],        
           "x-amazon-apigateway-integration": {
             "type": "mock",
             "responses": {
               "default": {
                 "statusCode": "200",
                 "responseParameters": {
                   "method.response.header.Content-Type": "'text/html'"
                 },
               }
             },
             "requestTemplates": {
               "application/json": "{\"statusCode\": 200}"
             },
             "passthroughBehavior": "when_no_match"
           }
         },
         ...
      }
   ```

1.  To use the access token for method authorization, change the above security definition to `{ "MyUserPool": [resource-server/scope, ...] }`:

   ```
     "paths": {
       "/": {
         "get": {
           "consumes": [
             "application/json"
           ],
           "produces": [
             "text/html"
           ],
           "responses": {
             "200": {
               "description": "200 response",
               "headers": {
                 "Content-Type": {
                   "type": "string"
                 }
               }
             }
           },
           "security": [
             {
               "MyUserPool": ["com.hamuta.movies/drama.view", "http://my.resource.com/file.read"]
             }
           ],        
           "x-amazon-apigateway-integration": {
             "type": "mock",
             "responses": {
               "default": {
                 "statusCode": "200",
                 "responseParameters": {
                   "method.response.header.Content-Type": "'text/html'"
                 },
               }
             },
             "requestTemplates": {
               "application/json": "{\"statusCode\": 200}"
             },
             "passthroughBehavior": "when_no_match"
           }
         },
         ...
      }
   ```

1. If needed, you can set other API configuration settings by using the appropriate OpenAPI definitions or extensions\. For more information, see [Working with API Gateway extensions to OpenAPI](api-gateway-swagger-extensions.md)\.