# Use API Gateway Lambda Authorizers<a name="apigateway-use-lambda-authorizer"></a>

 An Amazon API Gateway *Lambda authorizer* \(formerly known as a *custom authorizer*\) is a Lambda function that you provide to control access to your API\. A Lambda authorizer uses bearer token authentication strategies, such as OAuth or SAML\. It can also use information described by headers, paths, query strings, stage variables, or context variables request parameters\. 

**Note**  
 Path parameters can be used to grant or deny permissions to invoke a method, but they cannot be used to define identity sources, which can be used as parts of an authorization policy caching key\. Only headers, query strings, stage variables, and context variables can be set as identity sources\. 

 When a client calls your API, API Gateway verifies whether a Lambda authorizer is configured for the API method\. If so, API Gateway calls the Lambda function\. In this call, API Gateway supplies the authorization token that is extracted from a specified request header for the token\-based authorizer, or passes in the incoming request parameters as the input \(for example, the `event` parameter\) to the request parameters\-based authorizer function\. 

![\[API Gateway Lambda authorization workflow\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/custom-auth-workflow.png)

You can implement various authorization strategies, such as JSON Web Token \(JWT\) verification and OAuth provider callout\. You can also implement a custom scheme based on incoming request parameter values, to return IAM policies that authorize the request\. If the returned policy is invalid or the permissions are denied, the API call does not succeed\. For a valid policy, API Gateway caches the returned policy, associated with the incoming token or identity source request parameters\. It then uses the cached policy for the current and subsequent requests, over a pre\-configured time\-to\-live \(TTL\) period of up to 3600 seconds\. You can set the TTL period to zero seconds to disable the policy caching\. The default TTL value is 300 seconds\. Currently, the maximum TTL value of 3600 seconds cannot be increased\. 

**Topics**
+ [Types of API Gateway Lambda Authorizers](#api-gateway-lambda-authorizer-types)
+ [Create an API Gateway Lambda Authorizer Lambda Function](#api-gateway-lambda-authorizer-lambda-function-create)
+ [Input to an Amazon API Gateway Lambda Authorizer](api-gateway-lambda-authorizer-input.md)
+ [Output from an Amazon API Gateway Lambda Authorizer](api-gateway-lambda-authorizer-output.md)
+ [Configure Lambda Authorizer Using the API Gateway Console](configure-api-gateway-lambda-authorization-with-console.md)
+ [Call an API with API Gateway Lambda Authorizers](call-api-with-api-gateway-lambda-authorization.md)
+ [Configure Cross\-Account Lambda Authorizer](apigateway-lambda-authorizer-cross-account-lambda-authorizer.md)

## Types of API Gateway Lambda Authorizers<a name="api-gateway-lambda-authorizer-types"></a>

API Gateway supports the following types of Lambda authorizers:
+ `TOKEN` type Lambda authorizers grant a caller permissions to invoke a given request using an authorization token passed in a header\. The token could be, for example, an OAuth token\.
+ `REQUEST` type Lambda authorizers grant a caller permissions to invoke a given request using request parameters, including headers, query strings, stage variables, or context parameters\.

**Note**  
For WebSocket APIs, only `REQUEST` type authorizers are supported\.

## Create an API Gateway Lambda Authorizer Lambda Function<a name="api-gateway-lambda-authorizer-lambda-function-create"></a>

 Before creating an API Gateway Lambda authorizer, you must first create the AWS Lambda function that implements the logic to authorize and, if necessary, to authenticate the caller\. You can do so in the Lambda console, using the code template available from the API Gateway Lambda Authorizer blueprint\. Or you can create one from scratch, following this [example in awslabs](https://github.com/awslabs/aws-apigateway-lambda-authorizer-blueprints)\. For illustration purposes, we explain how to create a simple Lambda function from scratch without using a blueprint\. In production code, you should follow the API Gateway Lambda Authorizer blueprint to implement your authorizer Lambda function\. 

 When creating the Lambda function for your API Gateway Lambda authorizer, you assign an execution role for the Lambda function if it calls other AWS services\. For the following example, the basic `AWSLambdaRole` suffices\. For more involved use cases, follow the [instructions](https://docs.aws.amazon.com/lambda/latest/dg/intro-permission-model.html#lambda-intro-execution-role) to grant permissions in an execution role for the Lambda function\. 

### Control a Lambda Function of a Lambda Authorizer<a name="api-gateway-lambda-authorizer-token-control-lambda-function"></a>

To grant IAM principals in the account that owns the API permission to call [authorizer:create](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/authorizer-create) or [create\-authorizer](https://docs.aws.amazon.com/cli/latest/reference/apigateway/create-authorizer.html) to control the Lambda function used in your Lambda authorizer, you can create the following IAM policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "apigateway:POST"
            ],
            "Resource": [
                "arn:aws:apigateway:region::/restapis/restapi_id/authorizers"
            ],
            //Create Authorizer operation is allowed only with the following Lambda function
            "Condition": {
                "StringEquals": {
                    "apigateway:AuthorizerUri": "arn:aws:lambda:region:account-id:function:lambda-function-name"
                }
            }
        }
    ]
}
```

### Create a Lambda Function for a Lambda Authorizer of the `TOKEN` type<a name="api-gateway-lambda-authorizer-token-lambda-function-create"></a>

 In the code editor of the Lambda console, enter the following Node\.js code as an example of the API Gateway Lambda authorizer of the `TOKEN` type\. 

```
// A simple TOKEN authorizer example to demonstrate how to use an authorization token 
// to allow or deny a request. In this example, the caller named 'user' is allowed to invoke 
// a request if the client-supplied token value is 'allow'. The caller is not allowed to invoke 
// the request if the token value is 'deny'. If the token value is 'Unauthorized', the function 
// returns the 'Unauthorized' error with an HTTP status code of 401. For any other token value, 
// the authorizer returns an 'Invalid token' error. 

exports.handler =  function(event, context, callback) {
    var token = event.authorizationToken;
    switch (token.toLowerCase()) {
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
            callback("Error: Invalid token"); 
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

 For Lambda authorizers of the `TOKEN` type, API Gateway passes the source token to the Lambda function as the `event.authorizationToken`\. Based on the value of this token, the preceding authorizer function returns an `Allow` IAM policy on a specified method if the token value is `'allow'`\. This permits a caller to invoke the specified method\. The caller receives a `200 OK` response\. The authorizer function returns a `Deny` policy against the specified method if the authorization token has a `'deny'` value\. This blocks the caller from calling the method\. The client receives a `403 Forbidden` response\. If the token is `'unauthorized'`, the client receives a `401 Unauthorized` response\. If the token is `'fail'` or anything else, the client receives a `500 Internal Server Error` response\. In both of the last two cases, no IAM policy is generated and the calls fail\. 

**Note**  
In production code, you may need to authenticate the user before granting authorizations\. If so, you can add authentication logic in the Lambda function as well\. Consult the provider\-specific documentation for instructions on how to call such an authentication provider\. 

 In addition to returning an IAM policy, the Lambda authorizer function must also return the caller's principal identifier\. It can optionally return a key\-value map named `context`, containing additional information that can be passed into the integration backend\. For more information about the authorizer's output format, see [Output from an Amazon API Gateway Lambda Authorizer](api-gateway-lambda-authorizer-output.md)\. 

 You can use the `context` map to return cached credentials from the authorizer to the backend, using an integration request mapping template\. This enables the backend to provide an improved user experience by using the cached credentials to reduce the need to access the secret keys and open the authorization tokens for every request\. 

 For the Lambda proxy integration, API Gateway passes the `context` object from a Lambda authorizer directly to the backend Lambda function as part of the input `event`\. You can retrieve the `context` key\-value pairs in the Lambda function by calling `$event.requestContext.authorizer.key`\. For the preceding Lambda authorizer example, *key* is `stringKey`, `numberKey`, or `booleanKey`\. Their values are stringified, for example, `"stringval"`, `"123"`, or `"true"`, respectively\. 

 Before going further, you may want to test the Lambda function from within the Lambda console\. To do this, configure the sample event to provide an input as described in [Input to an Amazon API Gateway Lambda Authorizer](api-gateway-lambda-authorizer-input.md) and verify the result by examining the output compatible with [Output from an Amazon API Gateway Lambda Authorizer](api-gateway-lambda-authorizer-output.md)\. The next subsection explains how to create a Lambda function of the `Request` authorizer\. 

### Create a Lambda Function of a Lambda Authorizer of the `REQUEST` type<a name="api-gateway-lambda-authorizer-request-lambda-function-create"></a>

 In the code editor of the Lambda console, enter the following Node\.js code for a simplified Lambda function as an example of the API Gateway Lambda authorizers of the `REQUEST` type\. 

```
exports.handler = function(event, context, callback) {        
    console.log('Received event:', JSON.stringify(event, null, 2));

    // A simple REQUEST authorizer example to demonstrate how to use request 
    // parameters to allow or deny a request. In this example, a request is  
    // authorized if the client-supplied HeaderAuth1 header, QueryString1 query parameter,
    // stage variable of StageVar1 and the accountId in the request context all match
    // specified values of 'headerValue1', 'queryValue1', 'stageValue1', and
    // '123456789012', respectively.

    // Retrieve request parameters from the Lambda function input:
    var headers = event.headers;
    var queryStringParameters = event.queryStringParameters;
    var pathParameters = event.pathParameters;
    var stageVariables = event.stageVariables;
    var requestContext = event.requestContext;
        
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
        && stageVariables.StageVar1 === "stageValue1"
        && requestContext.accountId === "123456789012") {
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

 This Lambda function of the `REQUEST` authorizer verifies the input request parameters to return an `Allow` IAM policy on a specified method if all the required parameter \(`HeaderAuth1`, `QueryString1`, `StageVar1`, and `accountId`\) values match the pre\-configured ones\. This permits a caller to invoke the specified method\. The caller receives a `200 OK` response\. Otherwise, the authorizer function returns an `Unauthorized` error, without generating any IAM policy\. 

The above example authorizer function in Node\.js illustrates the programming flow to create a Lambda authorizer of the `REQUEST` type, including parsing the input which is similar to parsing the [Lambda function input in the Lambda proxy integration](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-input-format), You can extend the implementation to other languages supported by Lambda, such as Java or Python\. For example to parse the input to a Lambda REQUEST authorizer in Java, see [Java Function for an API with Lambda Proxy Integration](api-gateway-create-api-as-simple-proxy-for-lambda.md#api-gateway-proxy-integration-lambda-function-java)\.

 Before going further, you may want to test the Lambda function from within the Lambda console\. To do this, configure the sample event to provide the input and verify the result by examining the output\. The next two sections explain the [Input to an Amazon API Gateway Lambda Authorizer](api-gateway-lambda-authorizer-input.md) and [Output from an Amazon API Gateway Lambda Authorizer](api-gateway-lambda-authorizer-output.md)\. 