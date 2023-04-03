# Creating a Lambda `REQUEST` authorizer function<a name="apigateway-websocket-api-lambda-auth"></a>

A Lambda authorizer function in WebSocket APIs is similar to that for [REST APIs](apigateway-use-lambda-authorizer.md#api-gateway-lambda-authorizer-lambda-function-create), with the following exceptions:
+  You can only use a Lambda authorizer function for the `$connect` route\. 
+ You cannot use path variables \(`event.pathParameters`\), because the path is fixed\.
+ `event.methodArn` is different from its REST API equivalent, because it has no HTTP method\. In the case of `$connect`, `methodArn` ends with `"$connect"`:

  ```
  arn:aws:execute-api:region:account-id:api-id/stage-name/$connect
  ```
+ The context variables in `event.requestContext` are different from those for REST APIs\.

 The following example shows an input to a `REQUEST` authorizer for a WebSocket API: 

```
{
    "type": "REQUEST",
    "methodArn": "arn:aws:execute-api:us-east-1:123456789012:abcdef123/default/$connect",
    "headers": {
        "Connection": "upgrade",
        "content-length": "0",
        "HeaderAuth1": "headerValue1",
        "Host": "abcdef123.execute-api.us-east-1.amazonaws.com",
        "Sec-WebSocket-Extensions": "permessage-deflate; client_max_window_bits",
        "Sec-WebSocket-Key": "...",
        "Sec-WebSocket-Version": "13",
        "Upgrade": "websocket",
        "X-Amzn-Trace-Id": "...",
        "X-Forwarded-For": "...",
        "X-Forwarded-Port": "443",
        "X-Forwarded-Proto": "https"
    },
    "multiValueHeaders": {
        "Connection": [
            "upgrade"
        ],
        "content-length": [
            "0"
        ],
        "HeaderAuth1": [
            "headerValue1"
        ],
        "Host": [
            "abcdef123.execute-api.us-east-1.amazonaws.com"
        ],
        "Sec-WebSocket-Extensions": [
            "permessage-deflate; client_max_window_bits"
        ],
        "Sec-WebSocket-Key": [
            "..."
        ],
        "Sec-WebSocket-Version": [
            "13"
        ],
        "Upgrade": [
            "websocket"
        ],
        "X-Amzn-Trace-Id": [
            "..."
        ],
        "X-Forwarded-For": [
            "..."
        ],
        "X-Forwarded-Port": [
            "443"
        ],
        "X-Forwarded-Proto": [
            "https"
        ]
    },
    "queryStringParameters": {
        "QueryString1": "queryValue1"
    },
    "multiValueQueryStringParameters": {
        "QueryString1": [
            "queryValue1"
        ]
    },
    "stageVariables": {},
    "requestContext": {
        "routeKey": "$connect",
        "eventType": "CONNECT",
        "extendedRequestId": "...",
        "requestTime": "19/Jan/2023:21:13:26 +0000",
        "messageDirection": "IN",
        "stage": "default",
        "connectedAt": 1674162806344,
        "requestTimeEpoch": 1674162806345,
        "identity": {
            "sourceIp": "..."
        },
        "requestId": "...",
        "domainName": "abcdef123.execute-api.us-east-1.amazonaws.com",
        "connectionId": "...",
        "apiId": "abcdef123"
    }
}
```

The following example Lambda authorizer function is a WebSocket version of the Lambda authorizer function for REST APIs in [Create an API Gateway Lambda authorizer function in the Lambda console](apigateway-use-lambda-authorizer.md#api-gateway-lambda-authorizer-lambda-function-create):

```
export const handler = function(event, context, callback) {
    console.log('Received event:', JSON.stringify(event, null, 2));

   // A simple REQUEST authorizer example to demonstrate how to use request 
   // parameters to allow or deny a request. In this example, a request is  
   // authorized if the client-supplied HeaderAuth1 header and QueryString1 query parameter
   // in the request context match the specified values of
   // of 'headerValue1' and 'queryValue1' respectively.

   // Retrieve request parameters from the Lambda function input:
   var headers = event.headers;
   var queryStringParameters = event.queryStringParameters;
   var stageVariables = event.stageVariables;
   var requestContext = event.requestContext;
       
   // Parse the input for the parameter values
   var tmp = event.methodArn.split(':');
   var apiGatewayArnTmp = tmp[5].split('/');
   var awsAccountId = tmp[4];
   var region = tmp[3];
   var ApiId = apiGatewayArnTmp[0];
   var stage = apiGatewayArnTmp[1];
   var route = apiGatewayArnTmp[2];
       
   // Perform authorization to return the Allow policy for correct parameters and 
   // the 'Unauthorized' error, otherwise.
   var authResponse = {};
   var condition = {};
    condition.IpAddress = {};
    
   if (headers.HeaderAuth1 === "headerValue1"
       && queryStringParameters.QueryString1 === "queryValue1") {
        callback(null, generateAllow('me', event.methodArn));
    }  else {
        callback("Unauthorized");
    }
}
    
// Helper function to generate an IAM policy
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

To configure the preceding Lambda function as a `REQUEST` authorizer function for a WebSocket API, follow the same procedure as for [REST APIs](configure-api-gateway-lambda-authorization-with-console.md)\.

To configure the `$connect` route to use this Lambda authorizer in the console, select or create the `$connect` route\. Choose the route request and choose your authorizer in the **Authorization** dropdown menu\.

To test the authorizer, you need to create a new connection\. Changing authorizer in `$connect` doesn't affect the already connected client\. When you connect to your WebSocket API, you need to provide values for any configured identity sources\. For example, you can connect by sending a valid query string and header using `wscat` as in the following example:

```
wscat -c 'wss://myapi.execute-api.us-east-1.amazonaws.com/beta?QueryString1=queryValue1' -H HeaderAuth1:headerValue1
```

If you attempt to connect without a valid identity value, you'll receive a `401` response:

```
wscat -c wss://myapi.execute-api.us-east-1.amazonaws.com/beta
error: Unexpected server response: 401
```