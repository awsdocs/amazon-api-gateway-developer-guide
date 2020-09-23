# Set up Lambda proxy integrations in API Gateway<a name="set-up-lambda-proxy-integrations"></a>

**Topics**
+ [Understand API Gateway Lambda proxy integration](#api-gateway-create-api-as-simple-proxy)
+ [Support for multi\-value headers and query string parameters](#apigateway-multivalue-headers-and-parameters)
+ [Set up a proxy resource with Lambda proxy integration](#api-gateway-set-up-lambda-proxy-integration-on-proxy-resource)
+ [Set up Lambda proxy integration using the AWS CLI](#set-up-lambda-proxy-integration-using-cli)
+ [Input format of a Lambda function for proxy integration](#api-gateway-simple-proxy-for-lambda-input-format)
+ [Output format of a Lambda function for proxy integration](#api-gateway-simple-proxy-for-lambda-output-format)

## Understand API Gateway Lambda proxy integration<a name="api-gateway-create-api-as-simple-proxy"></a>

Amazon API Gateway Lambda proxy integration is a simple, powerful, and nimble mechanism to build an API with a setup of a single API method\. The Lambda proxy integration allows the client to call a single Lambda function in the backend\. The function accesses many resources or features of other AWS services, including calling other Lambda functions\. 

 In Lambda proxy integration, when a client submits an API request, API Gateway passes to the integrated Lambda function the raw request as\-is, except that the order of the request parameters is not preserved\. This [request data](#api-gateway-simple-proxy-for-lambda-input-format) includes the request headers, query string parameters, URL path variables, payload, and API configuration data\. The configuration data can include current deployment stage name, stage variables, user identity, or authorization context \(if any\)\. The backend Lambda function parses the incoming request data to determine the response that it returns\. For API Gateway to pass the Lambda output as the API response to the client, the Lambda function must return the result in [this format](#api-gateway-simple-proxy-for-lambda-output-format)\. 

 Because API Gateway doesn't intervene very much between the client and the backend Lambda function for the Lambda proxy integration, the client and the integrated Lambda function can adapt to changes in each other without breaking the existing integration setup of the API\. To enable this, the client must follow application protocols enacted by the backend Lambda function\. 

 You can set up a Lambda proxy integration for any API method\. But a Lambda proxy integration is more potent when it is configured for an API method involving a generic proxy resource\. The generic proxy resource can be denoted by a special templated path variable of `{proxy+}`, the catch\-all `ANY` method placeholder, or both\. The client can pass the input to the backend Lambda function in the incoming request as request parameters or applicable payload\. The request parameters include headers, URL path variables, query string parameters, and the applicable payload\. The integrated Lambda function verifies all of the input sources before processing the request and responding to the client with meaningful error messages if any of the required input is missing\.

 When calling an API method integrated with the generic HTTP method of `ANY` and the generic resource of `{proxy+}`, the client submits a request with a particular HTTP method in place of `ANY`\. The client also specifies a particular URL path instead of `{proxy+}`, and includes any required headers, query string parameters, or an applicable payload\. 

 The following list summarizes runtime behaviors of different API methods with the Lambda proxy integration: 
+ `ANY /{proxy+}`: The client must choose a particular HTTP method, must set a particular resource path hierarchy, and can set any headers, query string parameters, and applicable payload to pass the data as input to the integrated Lambda function\. 
+ `ANY /res`: The client must choose a particular HTTP method and can set any headers, query string parameters, and applicable payload to pass the data as input to the integrated Lambda function\. 
+ `GET|POST|PUT|... /{proxy+}`: The client can set a particular resource path hierarchy, any headers, query string parameters, and applicable payload to pass the data as input to the integrated Lambda function\. 
+  `GET|POST|PUT|... /res/{path}/...`: The client must choose a particular path segment \(for the `{path}` variable\) and can set any request headers, query string parameters, and applicable payload to pass input data to the integrated Lambda function\.
+  `GET|POST|PUT|... /res`: The client can choose any request headers, query string parameters, and applicable payload to pass input data to the integrated Lambda function\.

 Both the proxy resource of `{proxy+}` and the custom resource of `{custom}` are expressed as templated path variables\. However `{proxy+}` can refer to any resource along a path hierarchy, while `{custom}` refers to a particular path segment only\. For example, a grocery store might organize its online product inventory by department names, produce categories, and product types\. The grocery store's website can then represent available products by the following templated path variables of custom resources: `/{department}/{produce-category}/{product-type}`\. For example, apples are represented by `/produce/fruit/apple` and carrots by `/produce/vegetables/carrot`\. It can also use `/{proxy+}` to represent any department, any produce category, or any product type that a customer can search for while shopping in the online store\. For example, `/{proxy+}` can refer to any of the following items: 
+ `/produce`
+ `/produce/fruit`
+ `/produce/vegetables/carrot`

 To let customers search for any available product, its produce category, and the associated store department, you can expose a single method of `GET /{proxy+}` with read\-only permissions\. Similarly, to allow a supervisor to update the `produce` department's inventory, you can set up another single method of `PUT /produce/{proxy+}` with read/write permissions\. To allow a cashier to update the running total of a vegetable, you can set up a `POST /produce/vegetables/{proxy+}` method with read/write permissions\. To let a store manager perform any possible action on any available product, the online store developer can expose the `ANY /{proxy+}` method with read/write permissions\. In any case, at run time, the customer or the employee must select a particular product of a given type in a chosen department, a specific produce category in a chosen department, or a specific department\. 

For more information about setting up API Gateway proxy integrations, see [Set up a proxy integration with a proxy resource](api-gateway-set-up-simple-proxy.md)\. 

 Proxy integration requires that the client have more detailed knowledge of the backend requirements\. Therefore, to ensure optimal app performance and user experience, the backend developer must communicate clearly to the client developer the requirements of the backend, and provide a robust error feedback mechanism when the requirements are not met\. 

## Support for multi\-value headers and query string parameters<a name="apigateway-multivalue-headers-and-parameters"></a>

API Gateway supports multiple headers and query string parameters that have the same name\. Multi\-value headers as well as single\-value headers and parameters can be combined in the same requests and responses\. For more information, see [Input format of a Lambda function for proxy integration](#api-gateway-simple-proxy-for-lambda-input-format) and [Output format of a Lambda function for proxy integration](#api-gateway-simple-proxy-for-lambda-output-format)\.

## Set up a proxy resource with Lambda proxy integration<a name="api-gateway-set-up-lambda-proxy-integration-on-proxy-resource"></a>

To set up a proxy resource with the Lambda proxy integration type, create an API resource with a greedy path parameter \(for example, `/parent/{proxy+}`\) and integrate this resource with a Lambda function backend \(for example, `arn:aws:lambda:us-west-2:123456789012:function:SimpleLambda4ProxyResource`\) on the `ANY` method\. The greedy path parameter must be at the end of the API resource path\. As with a non\-proxy resource, you can set up the proxy resource by using the API Gateway console, importing an OpenAPI definition file, or calling the API Gateway REST API directly\.

The following OpenAPI API definition file shows an example of an API with a proxy resource that is integrated with a Lambda function named `SimpleLambda4ProxyResource`\.

------
#### [ OpenAPI 3\.0 ]

```
{
   "openapi": "3.0.0",
   "info": {
      "version": "2016-09-12T17:50:37Z",
      "title": "ProxyIntegrationWithLambda"
   },
   "paths": {
      "/{proxy+}": {
         "x-amazon-apigateway-any-method": {
            "parameters": [
               {
                  "name": "proxy",
                  "in": "path",
                  "required": true,
                  "schema": {
                     "type": "string"
                  }
               }
            ],
            "responses": {},
            "x-amazon-apigateway-integration": {
               "responses": {
                  "default": {
                     "statusCode": "200"
                  }
               },
               "uri": "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:SimpleLambda4ProxyResource/invocations",
               "passthroughBehavior": "when_no_match",
               "httpMethod": "POST",
               "cacheNamespace": "roq9wj",
               "cacheKeyParameters": [
                  "method.request.path.proxy"
               ],
               "type": "aws_proxy"
            }
         }
      }
   },
   "servers": [
      {
         "url": "https://gy415nuibc.execute-api.us-east-1.amazonaws.com/{basePath}",
         "variables": {
            "basePath": {
              "default": "/testStage"
            }
         }
      }
   ]
}
```

------
#### [ OpenAPI 2\.0 ]

```
{
  "swagger": "2.0",
  "info": {
    "version": "2016-09-12T17:50:37Z",
    "title": "ProxyIntegrationWithLambda"
  },
  "host": "gy415nuibc.execute-api.us-east-1.amazonaws.com",
  "basePath": "/testStage",
  "schemes": [
    "https"
  ],
  "paths": {
    "/{proxy+}": {
      "x-amazon-apigateway-any-method": {
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "proxy",
            "in": "path",
            "required": true,
            "type": "string"
          }
        ],
        "responses": {},
        "x-amazon-apigateway-integration": {
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "uri": "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:SimpleLambda4ProxyResource/invocations",
          "passthroughBehavior": "when_no_match",
          "httpMethod": "POST",
          "cacheNamespace": "roq9wj",
          "cacheKeyParameters": [
            "method.request.path.proxy"
          ],
          "type": "aws_proxy"
        }
      }
    }
  }
}
```

------

In Lambda proxy integration, at run time, API Gateway maps an incoming request into the input `event` parameter of the Lambda function\. The input includes the request method, path, headers, any query string parameters, any payload, associated context, and any defined stage variables\. The input format is explained in [Input format of a Lambda function for proxy integration](#api-gateway-simple-proxy-for-lambda-input-format)\. For API Gateway to map the Lambda output to HTTP responses successfully, the Lambda function must output the result in the format described in [Output format of a Lambda function for proxy integration](#api-gateway-simple-proxy-for-lambda-output-format)\. 

In Lambda proxy integration of a proxy resource through the `ANY` method, the single backend Lambda function serves as the event handler for all requests through the proxy resource\. For example, to log traffic patterns, you can have a mobile device send its location information of state, city, street, and building by submitting a request with `/state/city/street/house` in the URL path for the proxy resource\. The backend Lambda function can then parse the URL path and insert the location tuples into a DynamoDB table\.

## Set up Lambda proxy integration using the AWS CLI<a name="set-up-lambda-proxy-integration-using-cli"></a>

In this section, we show how to use AWS CLI to set up an API with the Lambda proxy integration\. 

**Note**  
For detailed instructions for using the API Gateway console to configure a proxy resource with the Lambda proxy integration, see [Tutorial: Build a Hello World REST API with Lambda proxy integration](api-gateway-create-api-as-simple-proxy-for-lambda.md)\.

As an example, we use the following sample Lambda function as the backend of the API:

```
exports.handler = function(event, context, callback) {
    console.log('Received event:', JSON.stringify(event, null, 2));
    var res ={
        "statusCode": 200,
        "headers": {
            "Content-Type": "*/*"
        }
    };
    var greeter = 'World';
    if (event.greeter && event.greeter!=="") {
        greeter =  event.greeter;
    } else if (event.body && event.body !== "") {
        var body = JSON.parse(event.body);
        if (body.greeter && body.greeter !== "") {
            greeter = body.greeter;
        }
    } else if (event.queryStringParameters && event.queryStringParameters.greeter && event.queryStringParameters.greeter !== "") {
        greeter = event.queryStringParameters.greeter;
    } else if (event.multiValueHeaders && event.multiValueHeaders.greeter && event.multiValueHeaders.greeter != "") {
        greeter = event.multiValueHeaders.greeter.join(" and ");
    } else if (event.headers && event.headers.greeter && event.headers.greeter != "") {
        greeter = event.headers.greeter;
    } 
    
    res.body = "Hello, " + greeter + "!";
    callback(null, res);
};
```

Comparing this to [the Lambda custom integration setup](set-up-lambda-custom-integrations.md#set-up-lambda-custom-integration-using-cli), the input to this Lambda function can be expressed in the request parameters and body\. You have more latitude to allow the client to pass the same input data\. Here, the client can pass the greeter's name in as a query string parameter, a header, or a body property\. The function can also support the Lambda custom integration\. The API setup is simpler\. You do not configure the method response or integration response at all\.

**To set up a Lambda proxy integration using the AWS CLI**

1.  Call the `create-rest-api` command to create an API:

   ```
   aws apigateway create-rest-api --name 'HelloWorld (AWS CLI)' --region us-west-2
   ```

   Note the resulting API's `id` value \(`te6si5ach7`\) in the response:

   ```
   {
       "name": "HelloWorldProxy (AWS CLI)", 
       "id": "te6si5ach7", 
       "createdDate": 1508461860
   }
   ```

   You need the API `id` throughout this section\.

1.  Call the `get-resources` command to get the root resource `id`:

   ```
   aws apigateway get-resources --rest-api-id te6si5ach7 --region us-west-2
   ```

   The successful response is shown as follows:

   ```
   {
       "items": [
           {
               "path": "/", 
               "id": "krznpq9xpg"
           }
       ]
   }
   ```

   Note the root resource `id` value \(`krznpq9xpg`\)\. You need it in the next step and later\.

1.  Call `create-resource` to create an API Gateway [Resource](https://docs.aws.amazon.com/apigateway/api-reference/resource/resource/) of `/greeting`:

   ```
   aws apigateway create-resource --rest-api-id te6si5ach7 \
         --region us-west-2 \
         --parent-id krznpq9xpg \
         --path-part {proxy+}
   ```

   The successful response is similar to the following:

   ```
   {
       "path": "/{proxy+}", 
       "pathPart": "{proxy+}", 
       "id": "2jf6xt", 
       "parentId": "krznpq9xpg"
   }
   ```

   Note the resulting `{proxy+}` resource's `id` value \(`2jf6xt`\)\. You need it to create a method on the `/{proxy+}` resource in the next step\.

1.  Call `put-method` to create an `ANY` method request of `ANY /{proxy+}`:

   ```
   aws apigateway put-method --rest-api-id te6si5ach7 \
          --region us-west-2 \
          --resource-id 2jf6xt \
          --http-method ANY \
          --authorization-type "NONE"
   ```

   The successful response is similar to the following:

   ```
   {
       "apiKeyRequired": false, 
       "httpMethod": "ANY", 
       "authorizationType": "NONE"
   }
   ```

   This API method allows the client to receive or send greetings from the Lambda function at the backend\. 

1.  Call `put-integration` to set up the integration of the `ANY /{proxy+}` method with a Lambda function, named `HelloWorld`\. This function responds to the request with a message of `"Hello, {name}!"`, if the `greeter` parameter is provided, or `"Hello, World!"`, if the query string parameter is not set\.

   ```
   aws apigateway put-integration \
           --region us-west-2
           --rest-api-id vaz7da96z6 \
           --resource-id 2jf6xt \
           --http-method ANY \
           --type AWS_PROXY \
           --integration-http-method POST \
           --uri arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:HelloWorld/invocations \
           --credentials arn:aws:iam::123456789012:role/apigAwsProxyRole
   ```
**Important**  
For Lambda integrations, you must use the HTTP method of `POST` for the integration request, according to the [specification of the Lambda service action for function invocations](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html)\. The IAM role of `apigAwsProxyRole` must have policies allowing the `apigateway` service to invoke Lambda functions\. For more information about IAM permissions, see [ API Gateway permissions model for invoking an API](permissions.md#api-gateway-control-access-iam-permissions-model-for-calling-api)\.

   The successful output is similar to the following:

   ```
   {
       "passthroughBehavior": "WHEN_NO_MATCH", 
       "cacheKeyParameters": [], 
       "uri": "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:1234567890:function:HelloWorld/invocations", 
       "httpMethod": "POST", 
       "cacheNamespace": "vvom7n", 
       "credentials": "arn:aws:iam::1234567890:role/apigAwsProxyRole", 
       "type": "AWS_PROXY"
   }
   ```

   Instead of supplying an IAM role for `credentials`, you can call the [add\-permission](https://docs.aws.amazon.com/cli/latest/reference/lambda/add-permission.html) command to add resource\-based permissions\. This is what the API Gateway console does\. 

1.  Call `create-deployment` to deploy the API to a `test` stage:

   ```
   aws apigateway create-deployment --rest-api-id te6si5ach7 --stage-name test
   ```

1.  Test the API using the following cURL commands in a terminal\.

   Calling the API with the query string parameter of `?greeter=jane`:

   ```
   curl -X GET 'https://te6si5ach7.execute-api.us-west-2.amazonaws.com/test/greeting?greeter=jane' \
     -H 'authorization: AWS4-HMAC-SHA256 Credential={access_key}/20171020/us-west-2/execute-api/aws4_request, \
         SignedHeaders=content-type;host;x-amz-date, Signature=f327...5751'
   ```

   Calling the API with a header parameter of `greeter:jane`:

   ```
   curl -X GET https://te6si5ach7.execute-api.us-west-2.amazonaws.com/test/hi \
     -H 'authorization: AWS4-HMAC-SHA256 Credential={access_key}/20171020/us-west-2/execute-api/aws4_request, \
         SignedHeaders=content-type;host;x-amz-date, Signature=f327...5751' \
     -H 'content-type: application/json' \
     -H 'greeter: jane'
   ```

   Calling the API with a body of `{"greeter":"jane"}`:

   ```
   curl -X POST https://te6si5ach7.execute-api.us-west-2.amazonaws.com/test \
     -H 'authorization: AWS4-HMAC-SHA256 Credential={access_key}/20171020/us-west-2/execute-api/aws4_request, \
         SignedHeaders=content-type;host;x-amz-date, Signature=f327...5751' 
     -H 'content-type: application/json' \
     -d '{ "greeter": "jane" }'
   ```

   In all the cases, the output is a 200 response with the following response body:

   ```
   Hello, jane!
   ```

## Input format of a Lambda function for proxy integration<a name="api-gateway-simple-proxy-for-lambda-input-format"></a>

In Lambda proxy integration, API Gateway maps the entire client request to the input `event` parameter of the backend Lambda function as follows: 

```
{
    "resource": "Resource path",
    "path": "Path parameter",
    "httpMethod": "Incoming request's method name"
    "headers": {String containing incoming request headers}
    "multiValueHeaders": {List of strings containing incoming request headers}
    "queryStringParameters": {query string parameters }
    "multiValueQueryStringParameters": {List of query string parameters}
    "pathParameters":  {path parameters}
    "stageVariables": {Applicable stage variables}
    "requestContext": {Request context, including authorizer-returned key-value pairs}
    "body": "A JSON string of the request payload."
    "isBase64Encoded": "A boolean flag to indicate if the applicable request payload is Base64-encode"
}
```

**Note**  
In the input:  
The `headers` key can only contain single\-value headers\.
The `multiValueHeaders` key can contain multi\-value headers as well as single\-value headers\.
If you specify values for both `headers` and `multiValueHeaders`, API Gateway merges them into a single list\. If the same key\-value pair is specified in both, only the values from `multiValueHeaders` will appear in the merged list\.

The following `POST` request shows an API deployed to `testStage` with a stage variable of `stageVariableName=stageVariableValue`:

```
POST /testStage/hello/world?name=me HTTP/1.1
Host: gy415nuibc.execute-api.us-east-1.amazonaws.com
Content-Type: application/json
headerName: headerValue

{
    "a": 1
}
```

This request produces the following response payload, which contains the output returned from the backend Lambda function, where `input` was set to the `event` parameter to the Lambda function\.

```
{
  "message": "Hello me!",
  "input": {
    "resource": "/{proxy+}",
    "path": "/hello/world",
    "httpMethod": "POST",
    "headers": {
      "Accept": "*/*",
      "Accept-Encoding": "gzip, deflate",
      "cache-control": "no-cache",
      "CloudFront-Forwarded-Proto": "https",
      "CloudFront-Is-Desktop-Viewer": "true",
      "CloudFront-Is-Mobile-Viewer": "false",
      "CloudFront-Is-SmartTV-Viewer": "false",
      "CloudFront-Is-Tablet-Viewer": "false",
      "CloudFront-Viewer-Country": "US",
      "Content-Type": "application/json",
      "headerName": "headerValue",
      "Host": "gy415nuibc.execute-api.us-east-1.amazonaws.com",
      "Postman-Token": "9f583ef0-ed83-4a38-aef3-eb9ce3f7a57f",
      "User-Agent": "PostmanRuntime/2.4.5",
      "Via": "1.1 d98420743a69852491bbdea73f7680bd.cloudfront.net (CloudFront)",
      "X-Amz-Cf-Id": "pn-PWIJc6thYnZm5P0NMgOUglL1DYtl0gdeJky8tqsg8iS_sgsKD1A==",
      "X-Forwarded-For": "54.240.196.186, 54.182.214.83",
      "X-Forwarded-Port": "443",
      "X-Forwarded-Proto": "https"
    },
    "multiValueHeaders":{
      'Accept':[
        "*/*"
      ],
      'Accept-Encoding':[
        "gzip, deflate"
      ],
      'cache-control':[
        "no-cache"
      ],
      'CloudFront-Forwarded-Proto':[
        "https"
      ],
      'CloudFront-Is-Desktop-Viewer':[
        "true"
      ],
      'CloudFront-Is-Mobile-Viewer':[
        "false"
      ],
      'CloudFront-Is-SmartTV-Viewer':[
        "false"
      ],
      'CloudFront-Is-Tablet-Viewer':[
        "false"
      ],
      'CloudFront-Viewer-Country':[
        "US"
      ],
      '':[
        ""
      ],
      'Content-Type':[
        "application/json"
      ],
      'headerName':[
        "headerValue"
      ],
      'Host':[
        "gy415nuibc.execute-api.us-east-1.amazonaws.com"
      ],
      'Postman-Token':[
        "9f583ef0-ed83-4a38-aef3-eb9ce3f7a57f"
      ],
      'User-Agent':[
        "PostmanRuntime/2.4.5"
      ],
      'Via':[
        "1.1 d98420743a69852491bbdea73f7680bd.cloudfront.net (CloudFront)"
      ],
      'X-Amz-Cf-Id':[
        "pn-PWIJc6thYnZm5P0NMgOUglL1DYtl0gdeJky8tqsg8iS_sgsKD1A=="
      ],
      'X-Forwarded-For':[
        "54.240.196.186, 54.182.214.83"
      ],
      'X-Forwarded-Port':[
        "443"
      ],
      'X-Forwarded-Proto':[
        "https"
      ]
    },
    "queryStringParameters": {
      "name": "me",
      "multivalueName": "me"
    },
    "multiValueQueryStringParameters":{
      "name":[
        "me"
      ],
      "multivalueName":[
        "you",
        "me"
      ]
    },
    "pathParameters": {
      "proxy": "hello/world"
    },
    "stageVariables": {
      "stageVariableName": "stageVariableValue"
    },
    "requestContext": {
      "accountId": "12345678912",
      "resourceId": "roq9wj",
      "stage": "testStage",
      "requestId": "deef4878-7910-11e6-8f14-25afc3e9ae33",
      "identity": {
        "cognitoIdentityPoolId": null,
        "accountId": null,
        "cognitoIdentityId": null,
        "caller": null,
        "apiKey": null,
        "sourceIp": "192.168.196.186",
        "cognitoAuthenticationType": null,
        "cognitoAuthenticationProvider": null,
        "userArn": null,
        "userAgent": "PostmanRuntime/2.4.5",
        "user": null
      },
      "resourcePath": "/{proxy+}",
      "httpMethod": "POST",
      "apiId": "gy415nuibc"
    },
    "body": "{\r\n\t\"a\": 1\r\n}",
    "isBase64Encoded": false
  }
}
```

In the input to the backend Lambda function, the `requestContext` object is a map of key\-value pairs\. In each pair, the key is the name of a [$context](api-gateway-mapping-template-reference.md#context-variable-reference) variable property, and the value is the value of that property\. API Gateway may add new keys to the map\.

Depending on the features that are enabled, the `requestContext` map may vary from API to API\. For example, in the preceding example, no authorization type is specified, so no `$context.authorizer.*` or `$context.identity.*` properties are present\. When an authorization type is specified, this causes API Gateway to pass authorized user information to the integration endpoint in a `requestContext.identity` object as follows:
+ When the authorization type is `AWS_IAM`, the authorized user information includes `$context.identity.*` properties\.
+ When the authorization type is `COGNITO_USER_POOLS` \(Amazon Cognito authorizer\), the authorized user information includes `$context.identity.cognito*` and `$context.authorizer.claims.*` properties\.
+ When the authorization type is `CUSTOM` \(Lambda authorizer\), the authorized user information includes `$context.authorizer.principalId` and other applicable `$context.authorizer.*` properties\.

The following shows an example of a `requestContext` that is passed to a Lambda proxy integration endpoint when the authorization type is set to `AWS_IAM`\.

```
{
    ...,
    "requestContext": {
        "requestTime": "20/Feb/2018:22:48:57 +0000",
        "path": "/test/",
        "accountId": "123456789012",
        "protocol": "HTTP/1.1",
        "resourceId": "yx5mhem7ye",
        "stage": "test",
        "requestTimeEpoch": 1519166937665,
        "requestId": "3c3ecbaa-1690-11e8-ae31-8f39f1d24afd",
        "identity": {
            "cognitoIdentityPoolId": null,
            "accountId": "123456789012",
            "cognitoIdentityId": null,
            "caller": "AIDAJ........4HCKVJZG",
            "sourceIp": "51.240.196.104",
            "accessKey": "IAM_user_access_key",
            "cognitoAuthenticationType": null,
            "cognitoAuthenticationProvider": null,
            "userArn": "arn:aws:iam::123456789012:user/alice",
            "userAgent": "PostmanRuntime/7.1.1",
            "user": "AIDAJ........4HCKVJZG"
        },
        "resourcePath": "/",
        "httpMethod": "GET",
        "apiId": "qr2gd9cfmf"
    },
    ...
}
```

## Output format of a Lambda function for proxy integration<a name="api-gateway-simple-proxy-for-lambda-output-format"></a>

In Lambda proxy integration, API Gateway requires the backend Lambda function to return output according to the following JSON format:

```
{
    "isBase64Encoded": true|false,
    "statusCode": httpStatusCode,
    "headers": { "headerName": "headerValue", ... },
    "multiValueHeaders": { "headerName": ["headerValue", "headerValue2", ...], ... },
    "body": "..."
}
```

In the output:
+ The `headers` and `multiValueHeaders` keys can be unspecified if no extra response headers are to be returned\.
+ The `headers` key can only contain single\-value headers\.
+ The `multiValueHeaders` key can contain multi\-value headers as well as single\-value headers\. You can use the `multiValueHeaders` key to specify all of your extra headers, including any single\-value ones\.
+ If you specify values for both `headers` and `multiValueHeaders`, API Gateway merges them into a single list\. If the same key\-value pair is specified in both, only the values from `multiValueHeaders` will appear in the merged list\.

To enable CORS for the Lambda proxy integration, you must add `Access-Control-Allow-Origin:domain-name` to the output `headers`\. `domain-name` can be `*` for any domain name\. The output `body` is marshalled to the frontend as the method response payload\. If `body` is a binary blob, you can encode it as a Base64\-encoded string by setting `isBase64Encoded` to `true` and configuring `*/*` as a **Binary Media Type**\. Otherwise, you can set it to `false` or leave it unspecified\.

**Note**  
For more information about enabling binary support, see [Enabling binary support using the API Gateway console](api-gateway-payload-encodings-configure-with-console.md)\. For an example Lambda function, see [Return binary media from a Lambda proxy integration](lambda-proxy-binary-media.md)\.

If the function output is of a different format, API Gateway returns a `502 Bad Gateway` error response\. 

To return a response in a Lambda function in Node\.js, you can use commands such as the following:
+ To return a successful result, call `callback(null, {"statusCode": 200, "body": "results"})`\.
+ To throw an exception, call `callback(new Error('internal server error'))`\.
+ For a client\-side error \(if, for example, a required parameter is missing\), you can call `callback(null, {"statusCode": 400, "body": "Missing parameters of ..."})` to return the error without throwing an exception\.

In a Lambda `async` function in Node\.js, the equivalent syntax would be:
+ To return a successful result, call `return {"statusCode": 200, "body": "results"}`\.
+ To throw an exception, call `throw new Error("internal server error")`\.
+ For a client\-side error \(if, for example, a required parameter is missing\), you can call `return {"statusCode": 400, "body": "Missing parameters of ..."}` to return the error without throwing an exception\.