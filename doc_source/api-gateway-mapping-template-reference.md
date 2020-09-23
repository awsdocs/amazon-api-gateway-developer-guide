# API Gateway mapping template and access logging variable reference<a name="api-gateway-mapping-template-reference"></a>

This section provides reference information for the variables and functions that Amazon API Gateway defines for use with data models, authorizers, mapping templates, and CloudWatch access logging\. For detailed information about how to use these variables and functions, see [Working with models and mapping templates](models-mappings.md)\.

**Topics**
+ [`$context` Variables for data models, authorizers, mapping templates, and CloudWatch access logging](#context-variable-reference)
+ [`$context` Variable template example](#context-variables-template-example)
+ [`$context` Variables for access logging only](#context-variable-reference-access-logging-only)
+ [`$input` Variables](#input-variable-reference)
+ [`$input` Variable template examples](#input-examples-mapping-templates)
+ [`$stageVariables`](#stagevariables-template-reference)
+ [`$util` Variables](#util-template-reference)

**Note**  
For `$method` and `$integration` variables, see [Amazon API Gateway API request and response data mapping reference](request-response-data-mappings.md)\.

## `$context` Variables for data models, authorizers, mapping templates, and CloudWatch access logging<a name="context-variable-reference"></a>

The following `$context` variables can be used in data models, authorizers, mapping templates, and CloudWatch access logging\.

For `$context` variables that can be used only in CloudWatch access logging, see [`$context` Variables for access logging only](#context-variable-reference-access-logging-only)\.


| Parameter | Description | 
| --- | --- | 
| $context\.accountId |  The API owner's AWS account ID\.  | 
| $context\.apiId |  The identifier API Gateway assigns to your API\.  | 
| $context\.authorizer\.claims\.property |  A property of the claims returned from the Amazon Cognito user pool after the method caller is successfully authenticated\. For more information, see [Control access to a REST API using Amazon Cognito User Pools as authorizer](apigateway-integrate-with-cognito.md)\.  Calling `$context.authorizer.claims` returns null\.   | 
| $context\.authorizer\.principalId |  The principal user identification associated with the token sent by the client and returned from an API Gateway Lambda authorizer \(formerly known as a custom authorizer\)\. For more information, see [Use API Gateway Lambda authorizers](apigateway-use-lambda-authorizer.md)\.  | 
| $context\.authorizer\.property |  The stringified value of the specified key\-value pair of the `context` map returned from an API Gateway Lambda authorizer function\. For example, if the authorizer returns the following `context` map:  <pre>"context" : {<br />  "key": "value",<br />  "numKey": 1,<br />  "boolKey": true<br />}</pre> calling `$context.authorizer.key` returns the `"value"` string, calling `$context.authorizer.numKey` returns the `"1"` string, and calling `$context.authorizer.boolKey` returns the `"true"` string\. For more information, see [Use API Gateway Lambda authorizers](apigateway-use-lambda-authorizer.md)\.  | 
| $context\.awsEndpointRequestId |  The AWS endpoint's request ID\.  | 
| $context\.domainName |  The full domain name used to invoke the API\. This should be the same as the incoming `Host` header\.  | 
| $context\.domainPrefix |  The first label of the `$context.domainName`\. This is often used as a caller/customer identifier\.  | 
| $context\.error\.message |  A string containing an API Gateway error message\. This variable can only be used for simple variable substitution in a [GatewayResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) body\-mapping template, which is not processed by the Velocity Template Language engine, and in access logging\. For more information, see [Monitoring WebSocket API execution with CloudWatch metrics](apigateway-websocket-api-logging.md) and [Setting up gateway responses to customize error responses](api-gateway-gatewayResponse-definition.md#customize-gateway-responses)\.  | 
| $context\.error\.messageString | The quoted value of $context\.error\.message, namely "$context\.error\.message"\. | 
| $context\.error\.responseType |  A [type](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/#responseType) of [GatewayResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/)\. This variable can only be used for simple variable substitution in a [GatewayResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) body\-mapping template, which is not processed by the Velocity Template Language engine, and in access logging\. For more information, see [Monitoring WebSocket API execution with CloudWatch metrics](apigateway-websocket-api-logging.md) and [Setting up gateway responses to customize error responses](api-gateway-gatewayResponse-definition.md#customize-gateway-responses)\.  | 
| $context\.error\.validationErrorString |  A string containing a detailed validation error message\.  | 
| $context\.extendedRequestId | The extended ID that API Gateway assigns to the API request, which contains more useful information for debugging/troubleshooting\. | 
| $context\.httpMethod |  The HTTP method used\. Valid values include: `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, and `PUT`\.  | 
| $context\.identity\.accountId |  The AWS account ID associated with the request\.  | 
| $context\.identity\.apiKey |  For API methods that require an API key, this variable is the API key associated with the method request\. For methods that don't require an API key, this variable is null\. For more information, see [Creating and using usage plans with API keys](api-gateway-api-usage-plans.md)\.  | 
| $context\.identity\.apiKeyId | The API key ID associated with an API request that requires an API key\. | 
| $context\.identity\.caller |  The principal identifier of the caller making the request\.  | 
| $context\.identity\.cognitoAuthenticationProvider |  A comma\-separated list of the Amazon Cognito authentication providers used by the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\. For information, see [Using Federated Identities](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html) in the *Amazon Cognito Developer Guide*\.  | 
| $context\.identity\.cognitoAuthenticationType |  The Amazon Cognito authentication type of the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\.  | 
| $context\.identity\.cognitoIdentityId |  The Amazon Cognito identity ID of the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\.  | 
| $context\.identity\.cognitoIdentityPoolId |  The Amazon Cognito identity pool ID of the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\.  | 
| $context\.identity\.principalOrgId |  The [AWS organization ID](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_org_details.html)\.  | 
| $context\.identity\.sourceIp |  The source IP address of the TCP connection making the request to API Gateway\.  You should not trust this value if there is any chance that the `X-Forwarded-For` header could be forged\.   | 
| $context\.identity\.user |  The principal identifier of the user making the request\. Used in Lambda authorizers\. For more information, see [Output from an Amazon API Gateway Lambda authorizer](api-gateway-lambda-authorizer-output.md)\.  | 
| $context\.identity\.userAgent |  The [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) header of the API caller\.  | 
| $context\.identity\.userArn |  The Amazon Resource Name \(ARN\) of the effective user identified after authentication\. For more information, see [https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html)\.  | 
| $context\.path | The request path\. For example, for a non\-proxy request URL of https://\{rest\-api\-id\.execute\-api\.\{region\}\.amazonaws\.com/\{stage\}/root/child, the $context\.path value is /\{stage\}/root/child\.  | 
| $context\.protocol | The request protocol, for example, HTTP/1\.1\. | 
| $context\.requestId |  The ID that API Gateway assigns to the API request\.  | 
| $context\.requestOverride\.header\.header\_name |  The request header override\. If this parameter is defined, it contains the headers to be used instead of the **HTTP Headers** that are defined in the **Integration Request** pane\. For more information, see [Use a mapping template to override an API's request and response parameters and status codes](apigateway-override-request-response-parameters.md)\.  | 
| $context\.requestOverride\.path\.path\_name |  The request path override\. If this parameter is defined, it contains the request path to be used instead of the **URL Path Parameters** that are defined in the **Integration Request** pane\. For more information, see [Use a mapping template to override an API's request and response parameters and status codes](apigateway-override-request-response-parameters.md)\.  | 
| $context\.requestOverride\.querystring\.querystring\_name |  The request query string override\. If this parameter is defined, it contains the request query strings to be used instead of the **URL Query String Parameters** that are defined in the **Integration Request** pane\. For more information, see [Use a mapping template to override an API's request and response parameters and status codes](apigateway-override-request-response-parameters.md)\.  | 
| $context\.responseOverride\.header\.header\_name | The response header override\. If this parameter is defined, it contains the header to be returned instead of the Response header that is defined as the Default mapping in the Integration Response pane\. For more information, see [Use a mapping template to override an API's request and response parameters and status codes](apigateway-override-request-response-parameters.md)\. | 
| $context\.responseOverride\.status | The response status code override\. If this parameter is defined, it contains the status code to be returned instead of the Method response status that is defined as the Default mapping in the Integration Response pane\. For more information, see [Use a mapping template to override an API's request and response parameters and status codes](apigateway-override-request-response-parameters.md)\. | 
| $context\.requestTime | The [CLF](https://httpd.apache.org/docs/1.3/logs.html#common)\-formatted request time \(dd/MMM/yyyy:HH:mm:ss \+\-hhmm\)\. | 
| $context\.requestTimeEpoch | The [Epoch](https://en.wikipedia.org/wiki/Unix_time)\-formatted request time\. | 
| $context\.resourceId |  The identifier that API Gateway assigns to your resource\.  | 
| $context\.resourcePath |  The path to your resource\. For example, for the non\-proxy request URI of `https://{rest-api-id.execute-api.{region}.amazonaws.com/{stage}/root/child`, The `$context.resourcePath` value is `/root/child`\. For more information, see [Tutorial: Build a REST API with HTTP non\-proxy integration](api-gateway-create-api-step-by-step.md)\.   | 
| $context\.stage |  The deployment stage of the API request \(for example, `Beta` or `Prod`\)\.  | 
| $context\.wafResponseCode |  The response received from [AWS WAF](https://docs.aws.amazon.com/waf/latest/developerguide/waf-chapter.html): `WAF_ALLOW` or `WAF_BLOCK`\. Will not be set if the stage is not associated with a web ACL\. For more information, see [Using AWS WAF to protect your APIs](apigateway-control-access-aws-waf.md)\.  | 
| $context\.webaclArn |  The complete ARN of the web ACL that is used to decide whether to allow or block the request\. Will not be set if the stage is not associated with a web ACL\. For more information, see [Using AWS WAF to protect your APIs](apigateway-control-access-aws-waf.md)\.  | 
| $context\.xrayTraceId |  The trace ID for the X\-Ray trace\. For more information, see [Setting up AWS X\-Ray with API Gateway REST APIs](apigateway-enabling-xray.md)\.  | 

## `$context` Variable template example<a name="context-variables-template-example"></a>

You may want to use `$context` variables in a mapping template if your API method passes structured data to a backend that requires the data to be in a particular format\.

The following example shows a mapping template that maps incoming `$context` variables to backend variables with slightly different names in an integration request payload:

**Note**  
Note that one of the variables is an API key\. This example assumes that the method has "require API key" enabled\.

```
{
    "stage" : "$context.stage",
    "request_id" : "$context.requestId",
    "api_id" : "$context.apiId",
    "resource_path" : "$context.resourcePath",
    "resource_id" : "$context.resourceId",
    "http_method" : "$context.httpMethod",
    "source_ip" : "$context.identity.sourceIp",
    "user-agent" : "$context.identity.userAgent",
    "account_id" : "$context.identity.accountId",
    "api_key" : "$context.identity.apiKey",
    "caller" : "$context.identity.caller",
    "user" : "$context.identity.user",
    "user_arn" : "$context.identity.userArn"
}
```

## `$context` Variables for access logging only<a name="context-variable-reference-access-logging-only"></a>

The following `$context` variables are available only for access logging\. For more information, see [Setting up CloudWatch logging for a REST API in API Gateway](set-up-logging.md)\. \(For WebSocket APIs, see [Monitoring WebSocket API execution with CloudWatch metrics](apigateway-websocket-api-logging.md)\.\)


| Parameter | Description | 
| --- | --- | 
| $context\.authorize\.error | The authorization error message\. | 
| $context\.authorize\.latency | The authorization latency in ms\. | 
| $context\.authorize\.status | The status code returned from an authorization attempt\. | 
| $context\.authorizer\.error | The error message returned from an authorizer\. | 
| $context\.authorizer\.integrationLatency | The authorizer latency in ms\. | 
| $context\.authorizer\.integrationStatus | The status code returned from a Lambda authorizer\. | 
| $context\.authorizer\.latency | The authorizer latency in ms\. | 
| $context\.authorizer\.requestId | The AWS endpoint's request ID\. | 
| $context\.authorizer\.status | The status code returned from an authorizer\. | 
| $context\.authenticate\.error | The error message returned from an authentication attempt\. | 
| $context\.authenticate\.latency | The authentication latency in ms\. | 
| $context\.authenticate\.status | The status code returned from an authentication attempt\. | 
| $context\.integration\.error | The error message returned from an integration\. Equivalent to $context\.integrationErrorMessage\. | 
| $context\.integration\.integrationStatus | For Lambda proxy integration, the status code returned from AWS Lambda, not from the backend Lambda function code\. | 
| $context\.integration\.latency | The integration latency in ms\. Equivalent to $context\.integrationLatency\. | 
| $context\.integration\.requestId | The AWS endpoint's request ID\. Equivalent to $context\.awsEndpointRequestId\. | 
| $context\.integration\.status | The status code returned from an integration\. For Lambda proxy integrations, this is the status code that your Lambda function code returns\. Equivalent to $context\.integrationStatus\. | 
| $context\.integrationErrorMessage |  A string that contains an integration error message\.  | 
| $context\.integrationLatency | The integration latency in ms\. | 
| $context\.integrationStatus | For Lambda proxy integration, this parameter represents the status code returned from AWS Lambda, not from the backend Lambda function code\. | 
| $context\.responseLatency | The response latency in ms\. | 
| $context\.responseLength | The response payload length\. | 
| $context\.status | The method response status\. | 
| $context\.waf\.error | The error message returned from AWS WAF\. | 
| $context\.waf\.latency | The AWS WAF latency in ms\. | 
| $context\.waf\.status | The status code returned from AWS WAF\. | 

## `$input` Variables<a name="input-variable-reference"></a>

The `$input` variable represents the method request payload and parameters to be processed by a mapping template\. It provides four functions:


| Variable and function | Description | 
| --- | --- | 
| $input\.body | Returns the raw request payload as a string\. | 
| $input\.json\(x\) | This function evaluates a JSONPath expression and returns the results as a JSON string\. For example, `$input.json('$.pets')` returns a JSON string representing the `pets` structure\. For more information about JSONPath, see [JSONPath](http://goessner.net/articles/JsonPath/) or [JSONPath for Java](https://github.com/jayway/JsonPath)\. | 
| $input\.params\(\) | Returns a map of all the request parameters\. | 
| $input\.params\(x\) | Returns the value of a method request parameter from the path, query string, or header value \(searched in that order\), given a parameter name string `x`\. | 
| $input\.path\(x\) | Takes a JSONPath expression string \(`x`\) and returns a JSON object representation of the result\. This allows you to access and manipulate elements of the payload natively in [Apache Velocity Template Language \(VTL\)](https://velocity.apache.org/engine/devel/vtl-reference.html)\. For example, if the expression `$input.path('$.pets')` returns an object like this: <pre>[<br />  { <br />    "id": 1, <br />    "type": "dog", <br />    "price": 249.99 <br />  }, <br />  { <br />    "id": 2, <br />    "type": "cat", <br />    "price": 124.99 <br />  }, <br />  { <br />    "id": 3, <br />    "type": "fish", <br />    "price": 0.99 <br />  } <br />]</pre> `$input.path('$.pets').count()` would return `"3"`\. For more information about JSONPath, see [JSONPath](http://goessner.net/articles/JsonPath/) or [JSONPath for Java](https://github.com/jayway/JsonPath)\. | 

## `$input` Variable template examples<a name="input-examples-mapping-templates"></a>

### Parameter mapping template example<a name="context-example-param-map-template"></a>

 The following parameter\-mapping example passes all parameters, including `path`, `querystring`, and `header`, through to the integration endpoint via a JSON payload:

```
#set($allParams = $input.params())
{
  "params" : {
    #foreach($type in $allParams.keySet())
    #set($params = $allParams.get($type))
    "$type" : {
      #foreach($paramName in $params.keySet())
      "$paramName" : "$util.escapeJavaScript($params.get($paramName))"
      #if($foreach.hasNext),#end
      #end
    }
    #if($foreach.hasNext),#end
    #end
  }
}
```

In effect, this mapping template outputs all the request parameters in the payload as outlined as follows:

```
{
  "params" : {
     "path" : {    
       "path_name" : "path_value", 
       ...
     }
     "header" : {  
       "header_name" : "header_value",
       ...
     }
     "querystring" : {
       "querystring_name" : "querystring_value",
       ...
     }
   }
}
```

You may want to use the `$input` variable to get query strings and the request body with or without using models\. You may also want to get the parameter and the payload, or a subsection of the payload, into your Lambda function\. The following examples show how to do this\.

### Example JSON mapping template using `$input`<a name="input-example-json-mapping-template"></a>

The following example shows how to use a mapping to read a name from the query string and then include the entire POST body in an element:

```
{
    "name" : "$input.params('name')",
    "body" : $input.json('$') 
}
```

 If the JSON input contains unescaped characters that cannot be parsed by JavaScript, a 400 response may be returned\. Applying `$util.escapeJavaScript($input.json('$'))` above will ensure that the JSON input can be parsed properly\. 

### Example mapping template using `$input`<a name="input-example-inputs-mapping-template"></a>

The following example shows how to pass a JSONPath expression to the `json()` method\. You could also read a specific property of your request body object by using a period `(.)`, followed by your property name:

```
{
    "name" : "$input.params('name')",
    "body" : $input.json('$.mykey')  
}
```

 If a method request payload contains unescaped characters that cannot be parsed by JavaScript, you may get `400` response\. In this case, you need to call `$util.escapeJavaScript()` function in the mapping template, as shown as follows: 

```
{
    "name" : "$input.params('name')",
    "body" : $util.escapeJavaScript($input.json('$.mykey')) 
}
```

### Example request and response using `$input`<a name="input-example-request-and-response"></a>

Here's an example that uses all three functions:

**Request template:**

```
Resource: /things/{id}

With input template:
{
    "id" : "$input.params('id')",
    "count" : "$input.path('$.things').size()",
    "things" : $util.escapeJavaScript($input.json('$.things'))
}

POST /things/abc
{
    "things" : {
        "1" : {},
        "2" : {},
        "3" : {}
    }
}
```

**Response:**

```
{
  "id": "abc",
  "count": "3",
  "things": {
    "1": {},
    "2": {},
    "3": {}
  }
}
```

 For more mapping examples, see [Working with models and mapping templates](models-mappings.md)\. 

## `$stageVariables`<a name="stagevariables-template-reference"></a>

Stage variables can be used in parameter mapping and mapping templates and as placeholders in ARNs and URLs used in method integrations\. For more information, see [Setting up stage variables for a REST API deployment](stage-variables.md)\.


| Syntax | Description | 
| --- | --- | 
| $stageVariables\.<variable\_name> |  *<variable\_name>* represents a stage variable name\.  | 
| $stageVariables\['<variable\_name>'\] |  *<variable\_name>* represents any stage variable name\.  | 
| $\{stageVariables\['<variable\_name>'\]\} |  *<variable\_name>* represents any stage variable name\.  | 

## `$util` Variables<a name="util-template-reference"></a>

The `$util` variable contains utility functions for use in mapping templates\.

**Note**  
 Unless otherwise specified, the default character set is UTF\-8\.


| Function | Description | 
| --- | --- | 
| $util\.escapeJavaScript\(\) |  Escapes the characters in a string using JavaScript string rules\.  This function will turn any regular single quotes \(`'`\) into escaped ones \(`\'`\)\. However, the escaped single quotes are not valid in JSON\. Thus, when the output from this function is used in a JSON property, you must turn any escaped single quotes \(`\'`\) back to regular single quotes \(`'`\)\. This is shown in the following example:  <pre> $util.escapeJavaScript(data).replaceAll("\\'","'")</pre>   | 
| $util\.parseJson\(\) |   Takes "stringified" JSON and returns an object representation of the result\. You can use the result from this function to access and manipulate elements of the payload natively in Apache Velocity Template Language \(VTL\)\. For example, if you have the following payload:  <pre>{"errorMessage":"{\"key1\":\"var1\",\"key2\":{\"arr\":[1,2,3]}}"}</pre>  and use the following mapping template  <pre>#set ($errorMessageObj = $util.parseJson($input.path('$.errorMessage')))<br />{<br />   "errorMessageObjKey2ArrVal" : $errorMessageObj.key2.arr[0]<br />}<br /></pre> You will get the following output: <pre>{<br />   "errorMessageObjKey2ArrVal" : 1<br />}<br /></pre>  | 
| $util\.urlEncode\(\) | Converts a string into "application/x\-www\-form\-urlencoded" format\. | 
| $util\.urlDecode\(\) | Decodes an "application/x\-www\-form\-urlencoded" string\. | 
| $util\.base64Encode\(\) | Encodes the data into a base64\-encoded string\. | 
| $util\.base64Decode\(\) | Decodes the data from a base64\-encoded string\. | 