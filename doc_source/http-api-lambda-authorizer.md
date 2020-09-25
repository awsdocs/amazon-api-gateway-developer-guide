# Working with AWS Lambda authorizers for HTTP APIs<a name="http-api-lambda-authorizer"></a>

You use a Lambda authorizer to use a Lambda function to control access to your HTTP API\. Then, when a client calls your API, API Gateway invokes your Lambda function\. API Gateway uses the response from your Lambda function to determine whether the client can access your API\.

## Payload format version<a name="http-api-lambda-authorizer.payload-format"></a>

The authorizer payload format version specifies the format of the data that API Gateway sends to a Lambda authorizer, and how API Gateway interprets the response from Lambda\. If you don't specify a payload format version, the AWS Management Console uses the latest version by default\. If you create a Lambda authorizer by using the AWS CLI, AWS CloudFormation, or an SDK, you must specify an `authorizerPayloadFormatVersion`\. The supported values are `1.0` and `2.0`\. 

 If you need compatibility with REST APIs, use version `1.0`\.

The following examples show the structure of each payload format version\.

------
#### [ 2\.0 ]

```
{
    "version": "2.0",
    "type": "REQUEST",
    "routeArn": "arn:aws:execute-api:us-east-1:123456789012:abcdef123/test/GET/request",
    "identitySource": ["user1", "123"],
    "routeKey": "$default",
    "rawPath": "/my/path",
    "rawQueryString": "parameter1=value1&parameter1=value2&parameter2=value",
    "cookies": [ "cookie1", "cookie2" ],
    "headers": {
        "Header1": "value1",
        "Header2": "value2"
    },
    "queryStringParameters": { "parameter1": "value1,value2", "parameter2": "value" },
    "requestContext": {
        "accountId": "123456789012",
        "apiId": "api-id",
        "domainName": "id.execute-api.us-east-1.amazonaws.com",
        "domainPrefix": "id",
        "http": {
          "method": "POST",
          "path": "/my/path",
          "protocol": "HTTP/1.1",
          "sourceIp": "IP",
          "userAgent": "agent"
        },
        "requestId": "id",
        "routeKey": "$default",
        "stage": "$default",
        "time": "12/Mar/2020:19:03:58 +0000",
        "timeEpoch": 1583348638390
    },
    "pathParameters": {"parameter1": "value1"},
    "stageVariables": {"stageVariable1": "value1", "stageVariable2": "value2"}
}
```

------
#### [ 1\.0 ]

```
{
    "version": "1.0",
    "type": "REQUEST",
    "methodArn": "arn:aws:execute-api:us-east-1:123456789012:abcdef123/test/GET/request",
    "identitySource": "user1,123",
    "authorizationToken": "user1,123",
    "resource": "/request",
    "path": "/request",
    "httpMethod": "GET",
    "headers": {
        "X-AMZ-Date": "20170718T062915Z",
        "Accept": "*/*",
        "HeaderAuth1": "headerValue1",
        "CloudFront-Viewer-Country": "US",
        "CloudFront-Forwarded-Proto": "https",
        "CloudFront-Is-Tablet-Viewer": "false",
        "CloudFront-Is-Mobile-Viewer": "false",
        "User-Agent": "..."
    },
    "queryStringParameters": {
        "QueryString1": "queryValue1"
    },
    "pathParameters": {},
    "stageVariables": {
        "StageVar1": "stageValue1"
    },
    "requestContext": {
        "path": "/request",
        "accountId": "123456789012",
        "resourceId": "05c7jb",
        "stage": "test",
        "requestId": "...",
        "identity": {
            "apiKey": "...",
            "sourceIp": "..."
        },
        "resourcePath": "/request",
        "httpMethod": "GET",
        "apiId": "abcdef123"
    }
}
```

------

## Lambda authorizer response format<a name="http-api-lambda-authorizer.payload-format-response"></a>

The payload format version also determines the structure of the response that you must return from your Lambda function\.

### Lambda function response for format 1\.0<a name="http-api-lambda-authorizer.v1"></a>

If you choose the `1.0` format version, Lambda authorizers must return an IAM policy that allows or denies access to your API route\. You can use standard IAM policy syntax in the policy\. For examples of IAM policies, see [ Control access for invoking an API](api-gateway-control-access-using-iam-policies-to-invoke-api.md)\. The `context` object is optional\. You can pass context properties to Lambda integrations or access logs by using `$context.authorizer.property`\. To learn more, see [Customizing HTTP API access logs](http-api-logging-variables.md)\.

**Example**  

```
{
  "principalId": "abcdef", // The principal user identification associated with the token sent by the client.
  "policyDocument": {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": "execute-api:Invoke",
        "Effect": "Allow|Deny",
        "Resource": "arn:aws:execute-api:{regionId}:{accountId}:{apiId}/{stage}/{httpVerb}/[{resource}/[{child-resources}]]"
      }
    ]
  },
  "context": {
    "exampleKey": "exampleValue"
  }
}
```

### Lambda function response for format 2\.0<a name="http-api-lambda-authorizer.v2"></a>

If you choose the `2.0` format version, you can return a Boolean value or an IAM policy that uses standard IAM policy syntax from your Lambda function\. To return a Boolean value, enable simple responses for the authorizer\. The following examples demonstrate the format that you must code your Lambda function to return\. The `context` object is optional\. You can pass context properties to Lambda integrations or access logs by using `$context.authorizer.property`\. To learn more, see [Customizing HTTP API access logs](http-api-logging-variables.md)\.

------
#### [ Simple response ]

```
{
  "isAuthorized": true/false,
  "context": {
    "exampleKey": "exampleValue"
  }
}
```

------
#### [ IAM policy ]

```
{
  "principalId": "abcdef", // The principal user identification associated with the token sent by the client.
  "policyDocument": {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": "execute-api:Invoke",
        "Effect": "Allow|Deny",
        "Resource": "arn:aws:execute-api:{regionId}:{accountId}:{apiId}/{stage}/{httpVerb}/[{resource}/[{child-resources}]]"
      }
    ]
  },
  "context": {
    "exampleKey": "exampleValue"
  }
}
```

------

## Example Lambda authorizer functions<a name="http-api-lambda-authorizer.example-code"></a>

The following example Node\.js Lambda functions demonstrate the required response formats you need to return from your Lambda function for the `2.0` payload format version\.

------
#### [ Simple response ]

```
exports.handler = async(event) => {
    let response = {
        "isAuthorized": false,
        "context": {
            "stringKey": "value",
            "numberKey": 1,
            "booleanKey": true,
            "arrayKey": ["value1", "value2"],
            "mapKey": {"value1": "value2"}
        }
    };
    
    if (event.headers.authorization === "secretToken") {
        response = {
            "isAuthorized": true,
            "context": {
                "stringKey": "value",
                "numberKey": 1,
                "booleanKey": true,
                "arrayKey": ["value1", "value2"],
                "mapKey": {"value1": "value2"}
            }
        };
    }

    return response;

};
```

------
#### [ IAM policy ]

```
exports.handler = async(event) => {
  if (event.headers.authorization == "secretToken") {
    console.log("allowed");
    return {
      "principalId": "abcdef", // The principal user identification associated with the token sent by the client.
      "policyDocument": {
        "Version": "2012-10-17",
        "Statement": [{
          "Action": "execute-api:Invoke",
          "Effect": "Allow",
          "Resource": event.routeArn
        }]
      },
      "context": {
        "stringKey": "value",
        "numberKey": 1,
        "booleanKey": true,
        "arrayKey": ["value1", "value2"],
        "mapKey": { "value1": "value2" }
      }
    };
  }
  else {
    console.log("denied");
    return {
      "principalId": "abcdef", // The principal user identification associated with the token sent by the client.
      "policyDocument": {
        "Version": "2012-10-17",
        "Statement": [{
          "Action": "execute-api:Invoke",
          "Effect": "Deny",
          "Resource": event.routeArn
        }]
      },
      "context": {
        "stringKey": "value",
        "numberKey": 1,
        "booleanKey": true,
        "arrayKey": ["value1", "value2"],
        "mapKey": { "value1": "value2" }
      }
    };
  }
};
}
```

------

## Identity sources<a name="http-api-lambda-authorizer.identity-sources"></a>

You can optionally specify identity sources for a Lambda authorizer\. Identity sources specify the location of data that's required to authorize a request\. For example, you can specify header or query string values as identity sources\. If you specify identity sources, clients must include them in the request\. If the client's request doesn't include the identity sources, API Gateway doesn't invoke your Lambda authorizer, and the client receives a `401` error\. The following identity sources are supported:


**Selection expressions**  

| Type | Example | Notes | 
| --- | --- | --- | 
| Header value | $request\.header\.name | Header names are case\-insensitive\. | 
| Query string value | $request\.querystring\.name | Query string names are case\-sensitive\. | 
| Context variable | $context\.variableName | The value of a supported [context variable](http-api-logging-variables.md)\. | 
| Stage variable | $stageVariables\.variableName | The value of a [stage variable](http-api-stages.md#http-api-stages.stage-variables)\. | 

## Caching authorizer responses<a name="http-api-lambda-authorizer.caching"></a>

You can enable caching for a Lambda authorizer by specifying an [authorizerResultTtlInSeconds](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-authorizers.html#apis-apiid-authorizers-prop-createauthorizerinput-authorizerresultttlinseconds)\. When caching is enabled for an authorizer, API Gateway uses the authorizer's identity sources as the cache key\. If a client specifies the same parameters in identity sources within the configured TTL, API Gateway uses the cached authorizer result, rather than invoking your Lambda function\.

To enable caching, your authorizer must have at least one identity source\.

If you enable simple responses for an authorizer, the authorizer's response fully allows or denies all API requests that match the cached identity source values\. For more granular permissions, disable simple responses and return an IAM policy\.

By default, API Gateway uses the cached authorizer response for all routes of an API that use the authorizer\. To cache responses per route, add `$context.routeKey` to your authorizer's identity sources\.

## Create a Lambda authorizer<a name="http-api-lambda-authorizer.example-create"></a>

When you create a Lambda authorizer, you specify the Lambda function for API Gateway to use\. You must grant API Gateway permission to invoke the Lambda function by using either the function's resource policy or an IAM role\. For this example, we update the resource policy for the function so that it grants API Gateway permission to invoke our Lambda function\.

```
aws apigatewayv2 create-authorizer \
    --api-id abcdef123 \
    --authorizer-type REQUEST \
    --identity-source '$request.header.Authorization' \
    --name lambda-authorizer \ 
    --authorizer-uri 'arn:aws:apigateway:us-west-2:lambda:path/2015-03-31/functions/arn:aws:lambda:us-west-2:123456789012:function:my-function/invocations' \
    --authorizer-payload-format-version '2.0' \
    --enable-simple-responses
```

The following command grants API Gateway permission to invoke your Lambda function\. If API Gateway doesn't have permission to invoke your function, clients receive a `500 Internal Server Error`\.

```
aws lambda add-permission \
    --function-name my-authorizer-function \
    --statement-id apigateway-invoke-permissions-abc123 \ 
    --action lambda:InvokeFunction \
    --principal apigateway.amazonaws.com \
    --source-arn "arn:aws:execute-api:us-west-2:123456789012:api-id/authorizers/authorizer-id"
```

After you've created an authorizer and granted API Gateway permission to invoke it, update your route to use the authorizer\.

```
aws apigatewayv2 update-route \
    --api-id abcdef123 \
    --route-id acd123 \
    --authorization-type CUSTOM \
    --authorizer-id def123
```

## Troubleshooting Lambda authorizers<a name="http-api-lambda-authorizer.troubleshooting"></a>

If API Gateway can't invoke your Lambda authorizer, or your Lambda authorizer returns a response in an invalid format, clients receive a `500 Internal Server Error`\.

To troubleshoot errors, [enable access logging](http-api-logging.md) for your API stage\. Include the `$context.authorizer.error` logging variable in your log format\.

If the logs indicate that API Gateway doesn't have permission to invoke your function, update your function's resource policy or provide an IAM role to grant API Gateway permission to invoke your authorizer\.

If the logs indicate that your Lambda function returns an invalid response, verify that your Lambda function returns a response in the [required format](#http-api-lambda-authorizer.payload-format-response)\.