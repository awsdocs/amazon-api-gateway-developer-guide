# Use API Gateway Lambda authorizers<a name="apigateway-use-lambda-authorizer"></a>

A *Lambda authorizer* \(formerly known as a *custom authorizer*\) is an API Gateway feature that uses a Lambda function to control access to your API\.

A Lambda authorizer is useful if you want to implement a custom authorization scheme that uses a bearer token authentication strategy such as OAuth or SAML, or that uses request parameters to determine the caller's identity\.

When a client makes a request to one of your API's methods, API Gateway calls your Lambda authorizer, which takes the caller's identity as input and returns an IAM policy as output\.

There are two types of Lambda authorizers:
+ A *token\-based* Lambda authorizer \(also called a `TOKEN` authorizer\) receives the caller's identity in a bearer token, such as a JSON Web Token \(JWT\) or an OAuth token\.
+ A *request parameter\-based* Lambda authorizer \(also called a `REQUEST` authorizer\) receives the caller's identity in a combination of headers, query string parameters, [`stageVariables`](api-gateway-mapping-template-reference.md#stagevariables-template-reference), and [`$context`](api-gateway-mapping-template-reference.md#context-variable-reference) variables\.

  For WebSocket APIs, only request parameter\-based authorizers are supported\.

It is possible to use an AWS Lambda function from an AWS account that is different from the one in which you created your Lambda authorizer function\. For more information, see [Configure a cross\-account Lambda authorizer](apigateway-lambda-authorizer-cross-account-lambda-authorizer.md)\.

To see an example of a \.NET Lambda authorizer, view the [Creating an API Gateway Custom Authorizer in \.NET Core 2\.0](http://www.youtube.com/watch?v=-0EG17oVxV0&feature=player_embedded) video\.

**Topics**
+ [Lambda authorizer Auth workflow](#api-gateway-lambda-authorizer-flow)
+ [Steps to create an API Gateway Lambda authorizer](#api-gateway-lambda-authorizer-create)
+ [Create an API Gateway Lambda authorizer function in the Lambda console](#api-gateway-lambda-authorizer-lambda-function-create)
+ [Configure a Lambda authorizer using the API Gateway console](configure-api-gateway-lambda-authorization-with-console.md)
+ [Input to an Amazon API Gateway Lambda authorizer](api-gateway-lambda-authorizer-input.md)
+ [Output from an Amazon API Gateway Lambda authorizer](api-gateway-lambda-authorizer-output.md)
+ [Call an API with API Gateway Lambda authorizers](call-api-with-api-gateway-lambda-authorization.md)
+ [Configure a cross\-account Lambda authorizer](apigateway-lambda-authorizer-cross-account-lambda-authorizer.md)

## Lambda authorizer Auth workflow<a name="api-gateway-lambda-authorizer-flow"></a>

The following diagram illustrates the authorization workflow for Lambda authorizers\.

![\[API Gateway Lambda authorization workflow\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/custom-auth-workflow.png)

**API Gateway Lambda authorization workflow**

1. The client calls a method on an API Gateway API method, passing a bearer token or request parameters\.

1. API Gateway checks whether a Lambda authorizer is configured for the method\. If it is, API Gateway calls the Lambda function\.

1. The Lambda function authenticates the caller by means such as the following:
   + Calling out to an OAuth provider to get an OAuth access token\.
   + Calling out to a SAML provider to get a SAML assertion\.
   + Generating an IAM policy based on the request parameter values\.
   + Retrieving credentials from a database\.

1. If the call succeeds, the Lambda function grants access by returning an output object containing at least an IAM policy and a principal identifier\.

1. API Gateway evaluates the policy\.
   + If access is denied, API Gateway returns a suitable HTTP status code, such as `403 ACCESS_DENIED`\.
   + If access is allowed, API Gateway executes the method\. If caching is enabled in the authorizer settings, API Gateway also caches the policy so that the Lambda authorizer function doesn't need to be invoked again\.

## Steps to create an API Gateway Lambda authorizer<a name="api-gateway-lambda-authorizer-create"></a>

To create a Lambda authorizer, you need to perform the following tasks:

1. Create the Lambda authorizer function in the Lambda console as described in [Create an API Gateway Lambda authorizer function in the Lambda console](#api-gateway-lambda-authorizer-lambda-function-create)\. You can use one of the blueprint examples as a starting point and customize the [input](api-gateway-lambda-authorizer-input.md) and [output](api-gateway-lambda-authorizer-output.md) as desired\.

1. Configure the Lambda function as an API Gateway authorizer and configure an API method to require it, as described in [Configure a Lambda authorizer using the API Gateway console](configure-api-gateway-lambda-authorization-with-console.md)\. Alternatively, if you need a cross\-account Lambda authorizer, see [Configure a cross\-account Lambda authorizer](apigateway-lambda-authorizer-cross-account-lambda-authorizer.md)\.
**Note**  
You can also configure an authorizer by using the AWS CLI or an AWS SDK\.

1. Test your authorizer by using [Postman](https://www.postman.com/) as described in [Call an API with API Gateway Lambda authorizers](call-api-with-api-gateway-lambda-authorization.md)\.

## Create an API Gateway Lambda authorizer function in the Lambda console<a name="api-gateway-lambda-authorizer-lambda-function-create"></a>

Before configuring a Lambda authorizer, you must first create the Lambda function that implements the logic to authorize and, if necessary, to authenticate the caller\. The Lambda console provides a Python blueprint, which you can use by choosing **Use a blueprint** and choosing the **api\-gateway\-authorizer\-python** blueprint\. Otherwise, you'll want to use one of the blueprints in the [awslabs](https://github.com/awslabs/aws-apigateway-lambda-authorizer-blueprints) GitHub repository as a starting point\.

For the example Lambda authorizer functions in this section, which don't call other services, you can use the built\-in [https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)\. When creating the Lambda function for your own API Gateway Lambda authorizer, you'll need to assign an IAM execution role to the Lambda function if it calls other AWS services\. To create the role, follow the instructions in [AWS Lambda Execution Role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)\.

### EXAMPLE: Create a token\-based Lambda authorizer function<a name="api-gateway-lambda-authorizer-token-lambda-function-create"></a>

To create a token\-based Lambda authorizer function, enter the following Node\.js code in the Lambda console and test it in the API Gateway console as follows\.

1. In the Lambda console, choose **Create function**\.

1. Choose **Author from scratch**\.

1. Enter a name for the function\.

1. Choose **Create function**\.

1. Copy/paste the following code into the code editor\.

   ```
   // A simple token-based authorizer example to demonstrate how to use an authorization token 
   // to allow or deny a request. In this example, the caller named 'user' is allowed to invoke 
   // a request if the client-supplied token value is 'allow'. The caller is not allowed to invoke 
   // the request if the token value is 'deny'. If the token value is 'unauthorized' or an empty
   // string, the authorizer function returns an HTTP 401 status code. For any other token value, 
   // the authorizer returns an HTTP 500 status code. 
   // Note that token values are case-sensitive.
   
   exports.handler =  function(event, context, callback) {
       var token = event.authorizationToken;
       switch (token) {
           case 'allow':
               callback(null, generatePolicy('user', 'Allow', event.methodArn));
               break;
           case 'deny':
               callback(null, generatePolicy('user', 'Deny', event.methodArn));
               break;
           case 'unauthorized':
               callback("Unauthorized");   // Return a 401 Unauthorized response
               break;
           default:
               callback("Error: Invalid token"); // Return a 500 Invalid token response
       }
   };
   
   // Help function to generate an IAM policy
   var generatePolicy = function(principalId, effect, resource) {
       var authResponse = {};
       
       authResponse.principalId = principalId;
       if (effect && resource) {
           var policyDocument = {};
           policyDocument.Version = '2012-10-17'; 
           policyDocument.Statement = [];
           var statementOne = {};
           statementOne.Action = 'execute-api:Invoke'; 
           statementOne.Effect = effect;
           statementOne.Resource = resource;
           policyDocument.Statement[0] = statementOne;
           authResponse.policyDocument = policyDocument;
       }
       
       // Optional output with custom properties of the String, Number or Boolean type.
       authResponse.context = {
           "stringKey": "stringval",
           "numberKey": 123,
           "booleanKey": true
       };
       return authResponse;
   }
   ```

1. Choose **Save**\.

1. In the API Gateway console, create a [simple API](api-gateway-create-api-from-example.md) if you don't already have one\.

1. Choose your API from the API list\.

1. Choose **Authorizers**\.

1. Choose **Create New Authorizer**\.

1. Enter a name for the authorizer\.

1. For **Type**, choose Lambda\.

1. For **Lambda Function**, choose the region where you created your Lambda authorizer function and choose the function name from the dropdown list\.

1. Leave **Lambda Invoke Role** blank\.

1. For **Lambda Event Payload**, choose **Token**\.

1. For **Token Source**, enter **authorizationToken**\.

1. Choose **Create**, and then choose **Grant & Create**\.

1. Choose **Test**\.

1. For the **authorizationToken** value, enter **allow**\.

1. Choose **Test**\.

In this example, when the API receives a method request, API Gateway passes the source token to this Lambda authorizer function in the `event.authorizationToken` attribute\. The Lambda authorizer function reads the token and acts as follows:
+ If the token value is `'allow'`, the authorizer function returns a `200 OK` HTTP response and an IAM policy that looks like the following, and the method request succeeds:

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": "execute-api:Invoke",
        "Effect": "Allow",
        "Resource": "arn:aws:execute-api:us-east-1:123456789012:ivdtdhp7b5/ESTestInvoke-stage/GET/"
      }
    ]
  }
  ```
+ If the token value is `'deny'`, the authorizer function returns a `403 Forbidden` HTTP response and a `Deny` IAM policy that looks like the following, and the method request fails:

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": "execute-api:Invoke",
        "Effect": "Deny",
        "Resource": "arn:aws:execute-api:us-east-1:123456789012:ivdtdhp7b5/ESTestInvoke-stage/GET/"
      }
    ]
  }
  ```
+ If the token value is `'unauthorized'` or an empty string, the authorizer function returns a `401 Unauthorized` HTTP response, and the method call fails\.
+ If the token is anything else, the client receives a `500 Invalid token` response, and the method call fails\.

**Note**  
In production code, you may need to authenticate the user before granting authorization\. If so, you can add authentication logic in the Lambda function as well by calling an authentication provider as directed in the documentation for that provider\.

In addition to returning an IAM policy, the Lambda authorizer function must also return the caller's principal identifier\. It can also optionally return a `context` object containing additional information that can be passed into the integration backend\. For more information, see [Output from an Amazon API Gateway Lambda authorizer](api-gateway-lambda-authorizer-output.md)\. 

### EXAMPLE: Create a request\-based Lambda authorizer function<a name="api-gateway-lambda-authorizer-request-lambda-function-create"></a>

To create a request\-based Lambda authorizer function, enter the following Node\.js code in the Lambda console and test it in the API Gateway console as follows\.

1. In the Lambda console, choose **Create function**\.

1. Choose **Author from scratch**\.

1. Enter a name for the function\.

1. Choose **Create function**\.

1. Copy/paste the following code into the code editor\.

   ```
   exports.handler = function(event, context, callback) {        
       console.log('Received event:', JSON.stringify(event, null, 2));
   
       // A simple request-based authorizer example to demonstrate how to use request 
       // parameters to allow or deny a request. In this example, a request is  
       // authorized if the client-supplied HeaderAuth1 header, QueryString1 
       // query parameter, and stage variable of StageVar1 all match
       // specified values of 'headerValue1', 'queryValue1', and 'stageValue1',
       // respectively.
   
       // Retrieve request parameters from the Lambda function input:
       var headers = event.headers;
       var queryStringParameters = event.queryStringParameters;
       var pathParameters = event.pathParameters;
       var stageVariables = event.stageVariables;
           
       // Parse the input for the parameter values
       var tmp = event.methodArn.split(':');
       var apiGatewayArnTmp = tmp[5].split('/');
       var awsAccountId = tmp[4];
       var region = tmp[3];
       var restApiId = apiGatewayArnTmp[0];
       var stage = apiGatewayArnTmp[1];
       var method = apiGatewayArnTmp[2];
       var resource = '/'; // root resource
       if (apiGatewayArnTmp[3]) {
           resource += apiGatewayArnTmp[3];
       }
           
       // Perform authorization to return the Allow policy for correct parameters and 
       // the 'Unauthorized' error, otherwise.
       var authResponse = {};
       var condition = {};
       condition.IpAddress = {};
        
       if (headers.HeaderAuth1 === "headerValue1"
           && queryStringParameters.QueryString1 === "queryValue1"
           && stageVariables.StageVar1 === "stageValue1") {
           callback(null, generateAllow('me', event.methodArn));
       }  else {
           callback("Unauthorized");
       }
   }
        
   // Help function to generate an IAM policy
   var generatePolicy = function(principalId, effect, resource) {
       // Required output:
       var authResponse = {};
       authResponse.principalId = principalId;
       if (effect && resource) {
           var policyDocument = {};
           policyDocument.Version = '2012-10-17'; // default version
           policyDocument.Statement = [];
           var statementOne = {};
           statementOne.Action = 'execute-api:Invoke'; // default action
           statementOne.Effect = effect;
           statementOne.Resource = resource;
           policyDocument.Statement[0] = statementOne;
           authResponse.policyDocument = policyDocument;
       }
       // Optional output with custom properties of the String, Number or Boolean type.
       authResponse.context = {
           "stringKey": "stringval",
           "numberKey": 123,
           "booleanKey": true
       };
       return authResponse;
   }
        
   var generateAllow = function(principalId, resource) {
       return generatePolicy(principalId, 'Allow', resource);
   }
        
   var generateDeny = function(principalId, resource) {
       return generatePolicy(principalId, 'Deny', resource);
   }
   ```

1. Choose **Save**\.

1. In the API Gateway console, create a [simple API](api-gateway-create-api-from-example.md) if you don't already have one\.

1. Choose your API from the API list\.

1. Choose **Authorizers**\.

1. Choose **Create New Authorizer**\.

1. Enter a name for the authorizer\.

1. For **Type**, choose Lambda\.

1. For **Lambda Function**, choose the region where you created your Lambda authorizer function and choose the function name from the dropdown list\.

1. Leave **Lambda Invoke Role** blank\.

1. For **Lambda Event Payload**, choose **Request**\.

1. Under **Identity Sources**, add a **Header** named **HeaderAuth1**, a **Query String** named **QueryString1**, and a **Stage Variable** named **StageVar1**\.

1. Choose **Create**, and then choose **Grant & Create**\.

1. Choose **Test**\.

1. For **HeaderAuth1**, enter **headerValue1**\. For **QueryString1**, enter **queryValue1**\. For **StageVar1**, enter **stageValue1**\.

1. Choose **Test**\.

In this example, the Lambda authorizer function checks the input parameters and acts as follows:
+ If all the required parameter values match the expected values, the authorizer function returns a `200 OK` HTTP response and an IAM policy that looks like the following, and the method request succeeds:

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": "execute-api:Invoke",
        "Effect": "Allow",
        "Resource": "arn:aws:execute-api:us-east-1:123456789012:ivdtdhp7b5/ESTestInvoke-stage/GET/"
      }
    ]
  }
  ```
+ Otherwise, the authorizer function returns a `401 Unauthorized` HTTP response, and the method call fails\.

**Note**  
In production code, you may need to authenticate the user before granting authorization\. If so, you can add authentication logic in the Lambda function as well by calling an authentication provider as directed in the documentation for that provider\.

In addition to returning an IAM policy, the Lambda authorizer function must also return the caller's principal identifier\. It can also optionally return a `context` object containing additional information that can be passed into the integration backend\. For more information, see [Output from an Amazon API Gateway Lambda authorizer](api-gateway-lambda-authorizer-output.md)\. 