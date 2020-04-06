# Input to an Amazon API Gateway Lambda authorizer<a name="api-gateway-lambda-authorizer-input"></a>

 For a Lambda authorizer \(formerly known as a custom authorizer\) of the `TOKEN` type, you must specify a custom header as the **Token Source** when you configure the authorizer for your API\. The API client must pass the required authorization token in that header in the incoming request\. Upon receiving the incoming method request, API Gateway extracts the token from the custom header\. It then passes the token as the `authorizationToken` property of the `event` object of the Lambda function, in addition to the method ARN as the `methodArn` property: 

```
{
    "type":"TOKEN",
    "authorizationToken":"{caller-supplied-token}",
    "methodArn":"arn:aws:execute-api:{regionId}:{accountId}:{apiId}/{stage}/{httpVerb}/[{resource}/[{child-resources}]]"
}
```

 In this example, the `type` property specifies the authorizer type, which is a `TOKEN` authorizer\. The `{caller-supplied-token}` originates from the authorization header in a client request\. The `methodArn` is the ARN of the incoming method request and is populated by API Gateway in accordance with the Lambda authorizer configuration\. 

 For the example `TOKEN` authorizer Lambda function shown in the preceding section, the *\{caller\-supplied\-token\}* string is `allow`, `deny`, `unauthorized`, or any other string value\. An empty string value is the same as `unauthorized`\. The following shows an example of such an input to obtain an `Allow` policy on the `GET` method of an API \(`ymy8tbxw7b`\) of the AWS account \(`123456789012`\) in any stage \(`*`\)\. 

```
{
    "type":"TOKEN",
    "authorizationToken":"allow",
    "methodArn":"arn:aws:execute-api:us-west-2:123456789012:ymy8tbxw7b/*/GET/"
}
```

 For a Lambda authorizer of the `REQUEST` type, API Gateway passes the required request parameters to the authorizer Lambda function as part of the `event` object\. The affected request parameters include headers, path parameters, query string parameters, stage variables, and some of request context variables\. The API caller can set the path parameters, headers, and query string parameters\. The API developer must set the stage variables during the API deployment and API Gateway provides the request context at run time\. 

**Note**  
Path parameters can be passed as request parameters to the Lambda authorizer function, but they cannot be used as identity sources\.

 The following example shows an input to a `REQUEST` authorizer for an API method \(`GET /request`\) with a proxy integration: 

```
{
    "type": "REQUEST",
    "methodArn": "arn:aws:execute-api:us-east-1:123456789012:s4x3opwd6i/test/GET/request",
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
        "User-Agent": "...",
        "X-Forwarded-Proto": "https",
        "CloudFront-Is-SmartTV-Viewer": "false",
        "Host": "....execute-api.us-east-1.amazonaws.com",
        "Accept-Encoding": "gzip, deflate",
        "X-Forwarded-Port": "443",
        "X-Amzn-Trace-Id": "...",
        "Via": "...cloudfront.net (CloudFront)",
        "X-Amz-Cf-Id": "...",
        "X-Forwarded-For": "..., ...",
        "Postman-Token": "...",
        "cache-control": "no-cache",
        "CloudFront-Is-Desktop-Viewer": "true",
        "Content-Type": "application/x-www-form-urlencoded"
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
        "apiId": "s4x3opwd6i"
    }
}
```

 The `requestContext` is a map of key\-value pairs and corresponds to the [$context](api-gateway-mapping-template-reference.md#context-variable-reference) variable\. Its outcome is API\-dependent\. API Gateway may add new keys to the map\. For more information about Lambda function input in Lambda proxy integration, see [Input format of a Lambda function for proxy integration](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-input-format)\. 