# Working with AWS Lambda proxy integrations for HTTP APIs<a name="http-api-develop-integrations-lambda"></a>

A Lambda proxy integration enables you to integrate an API route with a Lambda function\. When a client calls your API, API Gateway sends the request to the Lambda function and returns the function's response to the client\. For examples of creating an HTTP API, see [Creating an HTTP API](http-api-develop.md#http-api-examples)\.

## Payload format version<a name="http-api-develop-integrations-lambda.proxy-format"></a>

The payload format version specifies the format of the data that API Gateway sends to a Lambda integration, and how API Gateway interprets the response from Lambda\. If you don't specify a payload format version, the AWS Management Console uses the latest version by default\. If you create a Lambda integration by using the AWS CLI, AWS CloudFormation, or an SDK, you must specify a `payloadFormatVersion`\. The supported values are `1.0` and `2.0`\. 

The following examples show the structure of each payload format version\.

**Note**  
Format `2.0` doesn't have `multiValueHeaders` or `multiValueQueryStringParameters` fields\. Duplicate headers are combined with commas and included in the `headers` field\. Duplicate query strings are combined with commas and included in the `queryStringParameters` field\.  
Format `2.0` includes a new `cookies` field\. All cookie headers in the request are combined with commas and added to the `cookies` field\. In the response to the client, each cookie becomes a `set-cookie` header\.

------
#### [ 2\.0 ]

```
{
  "version": "2.0",
  "routeKey": "$default",
  "rawPath": "/my/path",
  "rawQueryString": "parameter1=value1&parameter1=value2&parameter2=value",
  "cookies": [
    "cookie1",
    "cookie2"
  ],
  "headers": {
    "Header1": "value1",
    "Header2": "value1,value2"
  },
  "queryStringParameters": {
    "parameter1": "value1,value2",
    "parameter2": "value"
  },
  "requestContext": {
    "accountId": "123456789012",
    "apiId": "api-id",
    "authorizer": {
      "jwt": {
        "claims": {
          "claim1": "value1",
          "claim2": "value2"
        },
        "scopes": [
          "scope1",
          "scope2"
        ]
      }
    },
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
  "body": "Hello from Lambda",
  "pathParameters": {
    "parameter1": "value1"
  },
  "isBase64Encoded": false,
  "stageVariables": {
    "stageVariable1": "value1",
    "stageVariable2": "value2"
  }
}
```

------
#### [ 1\.0 ]

```
{
  "version": "1.0",
  "resource": "/my/path",
  "path": "/my/path",
  "httpMethod": "GET",
  "headers": {
    "Header1": "value1",
    "Header2": "value2"
  },
  "multiValueHeaders": {
    "Header1": [
      "value1"
    ],
    "Header2": [
      "value1",
      "value2"
    ]
  },
  "queryStringParameters": {
    "parameter1": "value1",
    "parameter2": "value"
  },
  "multiValueQueryStringParameters": {
    "parameter1": [
      "value1",
      "value2"
    ],
    "parameter2": [
      "value"
    ]
  },
  "requestContext": {
    "accountId": "123456789012",
    "apiId": "id",
    "authorizer": {
      "claims": null,
      "scopes": null
    },
    "domainName": "id.execute-api.us-east-1.amazonaws.com",
    "domainPrefix": "id",
    "extendedRequestId": "request-id",
    "httpMethod": "GET",
    "identity": {
      "accessKey": null,
      "accountId": null,
      "caller": null,
      "cognitoAuthenticationProvider": null,
      "cognitoAuthenticationType": null,
      "cognitoIdentityId": null,
      "cognitoIdentityPoolId": null,
      "principalOrgId": null,
      "sourceIp": "IP",
      "user": null,
      "userAgent": "user-agent",
      "userArn": null
    },
    "path": "/my/path",
    "protocol": "HTTP/1.1",
    "requestId": "id=",
    "requestTime": "04/Mar/2020:19:15:17 +0000",
    "requestTimeEpoch": 1583349317135,
    "resourceId": null,
    "resourcePath": "/my/path",
    "stage": "$default"
  },
  "pathParameters": null,
  "stageVariables": null,
  "body": "Hello from Lambda!",
  "isBase64Encoded": true
}
```

------

## Lambda function response format<a name="http-api-develop-integrations-lambda.response"></a>

The payload format version determines the structure of the response that your Lambda function must return\.

### Lambda function response for format 1\.0<a name="http-api-develop-integrations-lambda.v1"></a>

With the `1.0` format version, Lambda integrations must return a response in the following format\.

**Example**  

```
{
    "isBase64Encoded": true|false,
    "statusCode": httpStatusCode,
    "headers": { "headerName": "headerValue", ... },
    "multiValueHeaders": { "headerName": ["headerValue", "headerValue2", ...], ... },
    "body": "..."
}
```

### Lambda function response for format 2\.0<a name="http-api-develop-integrations-lambda.v2"></a>

With the `2.0` format version, API Gateway can infer the response format for you\. API Gateway makes the following assumptions if your Lambda function returns valid JSON and doesn't return a `statusCode`:
+ `isBase64Encoded` is `false`\.
+ `statusCode` is `200`\.
+ `content-type` is `application/json`\.
+ `body` is the function's response\.

The following examples show the output of a Lambda function and API Gateway's interpretation\.


| Lambda function output | API Gateway interpretation | 
| --- | --- | 
|  <pre>"Hello from Lambda!"</pre>  |  <pre>{<br />  "isBase64Encoded": false,<br />  "statusCode": 200,<br />  "body": "Hello from Lambda!",<br />  "headers": {<br />    "Content-Type": "application/json"<br />  }<br />}</pre>  | 
|  <pre>{ "message": "Hello from Lambda!" }</pre>  |  <pre>{<br />  "isBase64Encoded": false,<br />  "statusCode": 200,<br />  "body": "{ \"message\": \"Hello from Lambda!\" }",<br />  "headers": {<br />    "Content-Type": "application/json"<br />  }<br />}</pre>  | 

To customize the response, your Lambda function should return a response with the following format\.

```
{
    "cookies" : ["cookie1", "cookie2"],
    "isBase64Encoded": true|false,
    "statusCode": httpStatusCode,
    "headers": { "headerName": "headerValue", ... },
    "body": "Hello from Lambda!"
}
```