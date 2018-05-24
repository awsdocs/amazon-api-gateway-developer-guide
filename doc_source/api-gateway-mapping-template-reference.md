# API Gateway Mapping Template Reference<a name="api-gateway-mapping-template-reference"></a>

Amazon API Gateway defines a set of variables and functions for working with models and mapping templates\. This document describes those functions and provides examples for working with input payloads\.

**Topics**
+ [Accessing the $context Variable](#context-variable-reference)
+ [Accessing the $input Variable](#input-variable-reference)
+ [Accessing the $stageVariables Variable](#stagevariables-template-reference)
+ [Accessing the $util Variable](#util-template-reference)

## Accessing the $context Variable<a name="context-variable-reference"></a>

The `$context` variable holds all the contextual information of your API call\.


**$context Variable Reference**  

| Parameter | Description | 
| --- | --- | 
| $context\.apiId |  The identifier API Gateway assigns to your API\.  | 
| $context\.authorizer\.claims\.property |  A property of the claims returned from the Amazon Cognito user pool after the method caller is successfully authenticated\.  Calling `$context.authorizer.claims` returns null\.   | 
| $context\.authorizer\.principalId |  The principal user identification associated with the token sent by the client and returned from an API Gateway Lambda authorizer \(formerly known as a custom authorizer\) Lambda function\.  | 
| $context\.authorizer\.property |  The stringified value of the specified key\-value pair of the `context` map returned from an API Gateway Lambda authorizer Lambda function\. For example, if the authorizer returns the following `context` map:  <pre>"context" : {<br />  "key": "value",<br />  "numKey": 1,<br />  "boolKey": true<br />}</pre> calling `$context.authorizer.key` returns the `"value"` string, calling `$context.authorizer.numKey` returns the `"1"` string, and calling `$context.authorizer.boolKey` returns the `"true"` string\.  | 
| $context\.httpMethod |  The HTTP method used\. Valid values include: `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, and `PUT`\.  | 
| $context\.error\.message |  A string of an API Gateway error message\. This variable can only be used for simple variable substitution in a [GatewayResponse](http://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) body\-mapping template, which is not processed by the Velocity Template Language engine\.  | 
| $context\.error\.messageString | The quoted value of $context\.error\.message, namely "$context\.error\.message"\. | 
| $context\.error\.responseType |  A [type](http://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/#responseType) of [GatewayResponse](http://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/)\. This variable can only be used for simple variable substitution in a [GatewayResponse](http://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) body\-mapping template, which is not processed by the Velocity Template Language engine\.   | 
| $context\.extendedRequestId | An automatically generated ID for the API call, which contains more useful information for debugging/troubleshooting\. | 
| $context\.identity\.accountId |  The AWS account ID associated with the request\.  | 
| $context\.identity\.apiKey |  The API owner key associated with key\-enabled API request\.  | 
| $context\.identity\.apiKeyId | The API key ID associated with the key\-enabled API request | 
| $context\.identity\.caller |  The principal identifier of the caller making the request\.  | 
| $context\.identity\.cognitoAuthenticationProvider |  The Amazon Cognito authentication provider used by the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\. For information related to this and the other Amazon Cognito `$context` variables, see [Using Federated Identities](http://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html) in the *Amazon Cognito Developer Guide*\.  | 
| $context\.identity\.cognitoAuthenticationType |  The Amazon Cognito authentication type of the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\.  | 
| $context\.identity\.cognitoIdentityId |  The Amazon Cognito identity ID of the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\.  | 
| $context\.identity\.cognitoIdentityPoolId |  The Amazon Cognito identity pool ID of the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\.  | 
| $context\.identity\.sourceIp |  The source IP address of the TCP connection making the request to API Gateway\.  | 
| $context\.identity\.user |  The principal identifier of the user making the request\.  | 
| $context\.identity\.userAgent |  The User Agent of the API caller\.  | 
| $context\.identity\.userArn |  The Amazon Resource Name \(ARN\) of the effective user identified after authentication\.  | 
| $context\.integrationLatency | The integration latency in ms, available for access logging only\. | 
| $context\.path | The request path\. For example, for the non\-proxy request URI of https://\{rest\-api\-id\.execute\-api\.\{region\}\.amazonaws\.com/\{stage\}/root/child, The $context\.path value is /\{stage\}/root/child\.  | 
| $context\.protocol | The request protocol, for example, HTTP/1\.1\. | 
| $context\.requestId |  An automatically generated ID for the API call\.  | 
| $context\.requestTime | The [CLF](https://httpd.apache.org/docs/1.3/logs.html#common)\-formatted request time \(dd/MMM/yyyy:HH:mm:ss \+\-hhmm\)\. | 
| $context\.requestTimeEpoch | The [Epoch](https://en.wikipedia.org/wiki/Unix_time)\-formatted request time\. | 
| $context\.resourceId |  The identifier API Gateway assigns to your resource\.  | 
| $context\.resourcePath |  The path to your resource\. For example, for the non\-proxy request URI of `https://{rest-api-id.execute-api.{region}.amazonaws.com/{stage}/root/child`, The `$context.resourcePath` value is `/root/child`\. For more information, see [Build an API with HTTP Custom Integration](api-gateway-create-api-step-by-step.md)\.   | 
| $context\.responseLength | The response payload length, available for access logging only\. | 
| $context\.responseLatency | The response latency in ms, available for access logging only\. | 
| $context\.status | The response status, available for access logging only\. | 
| $context\.stage |  The deployment stage of the API call \(for example, Beta or Prod\)\.  | 

### Example<a name="context-examples-mapping-templates"></a>

You may want to use the `$context` variable if you're using AWS Lambda as the target backend that the API method calls\. For example, you may want to perform two different actions depending on whether the stage is in Beta or in Prod\.

#### Context Variables Template Example<a name="context-variables-template-example"></a>

The following example shows a mapping template to map context variables to an integration request payload:

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

In the above example, the method is assumed to have an API key enabled\. If API key is not required on the method request, `api_key` will be null\. 

For requests of the `AWS_IAM` authorization type, you can pass the authorized user information to the integration endpoint with `$context.identity.*` properties\. For requests of the `COGNITO_USER_POOLS` authorization type, the authorized user information will also include `$context.identity.cognito*` and `$context.authorizer.claims.*` properties\. For requests using a Lambda authorizer, you can pass `$context.authorizer.principalId` and other applicable `$context.authorizer.*` properties as additional authorized user context to the integration endpoint\.

With a proxy integration, API Gateway passes the authorized identity information to the backend in the `requestContext.identity` object\. You do not set up any mapping template and, instead, parse the input to the integration backend explicitly\. The following shows an example of `requestContext` passed to a Lambda proxy integration endpoint when the authorization type is set to `AWS_IAM`\.

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

## Accessing the $input Variable<a name="input-variable-reference"></a>

The `$input` variable represents the input payload and parameters to be processed by your template\. It provides four functions:


**Function Reference**  

| Variable and Function | Description | 
| --- | --- | 
| $input\.body | Returns the raw payload as a string\. | 
| $input\.json\(x\) | This function evaluates a JSONPath expression and returns the results as a JSON string\. For example, `$input.json('$.pets')` will return a JSON string representing the pets structure\. For more information about JSONPath, see [JSONPath](http://goessner.net/articles/JsonPath/) or [JSONPath for Java](https://github.com/jayway/JsonPath)\. | 
| $input\.params\(\) | Returns a map of all the request parameters of your API call\. | 
| $input\.params\(x\) | Returns the value of a method request parameter from the path, query string, or header value \(in that order\) given a parameter name string x\. | 
| $input\.path\(x\) | Takes a JSONPath expression string \(x\) and returns an object representation of the result\. This allows you to access and manipulate elements of the payload natively in [Apache Velocity Template Language \(VTL\)](http://velocity.apache.org/engine/devel/vtl-reference-guide.html)\. For example, `$input.path('$.pets').size()` For more information about JSONPath, see [JSONPath](http://goessner.net/articles/JsonPath/) or [JSONPath for Java](https://github.com/jayway/JsonPath)\. | 

### Examples<a name="input-examples-mapping-templates"></a>

You may want to use the `$input` variable to get query strings and the request body with or without using models\. You may also want to get the parameter and the payload, or a subsection of the payload, into your AWS Lambda function\. The examples below show how to do this\.

#### Example JSON Mapping Template<a name="input-example-json-mapping-template"></a>

The following example shows how to use a mapping to read a name from the query string and then include the entire POST body in an element:

```
{
    "name" : "$input.params('name')",
    "body" : $input.json('$') 
}
```

 If the JSON input contains unescaped characters that cannot be parsed by JavaScript, a 400 response may be returned\. Applying `$util.escapeJavaScript($input.json('$'))` above will ensure that the JSON input can be parsed properly\. 

#### Example Inputs Mapping Template<a name="input-example-inputs-mapping-template"></a>

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

#### Param Mapping Template Example<a name="context-example-param-map-template"></a>

 The following parameter\-mapping example passes all parameters, including path, querystring and header, through to the integration endpoint via a JSON payload 

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
  "parameters" : {
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

#### Example Request and Response<a name="input-example-request-and-response"></a>

Here’s an example that uses all three functions:

**Request Template:**

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

 For more mapping examples, see [Create Models and Mapping Templates for Request and Response Mappings](models-mappings.md) 

## Accessing the $stageVariables Variable<a name="stagevariables-template-reference"></a>

The syntax for inserting a stage variable looks like this: `$stageVariables`\.


**$stageVariables Reference**  

| Syntax | Description | 
| --- | --- | 
| $stageVariables\.<variable\_name> |  *<variable\_name>* represents a stage variable name\.  | 
| $stageVariables\['<variable\_name>'\] |  *<variable\_name>* represents any stage variable name\.  | 
| $\{stageVariables\['<variable\_name>'\]\} |  *<variable\_name>* represents any stage variable name\.  | 

## Accessing the $util Variable<a name="util-template-reference"></a>

The `$util` variable contains utility functions for use in mapping templates\.

**Note**  
 Unless otherwise specified, the default character set is UTF\-8\.


**$util Variable Reference**  

| Function | Description | 
| --- | --- | 
| $util\.escapeJavaScript\(\) |  Escapes the characters in a string using JavaScript string rules\. This function will turn any regular single quotes \(`'`\) into escaped ones \(`\'`\)\. However, the escaped single quotes are not valid in JSON\. Thus, when the output from this function is used in a JSON property, you must turn any escaped single quotes \(`\'`\) back to regular single quotes \(`'`\)\. This is shown in the following example: <pre> $util.escapeJavaScript(data).replaceAll("\\'","'")</pre> | 
| $util\.parseJson\(\) |   Takes "stringified" JSON and returns an object representation of the result\. You can use the result from this function to access and manipulate elements of the payload natively in Apache Velocity Template Language \(VTL\)\. For example, if you have the following payload:  <pre>{"errorMessage":"{\"key1\":\"var1\",\"key2\":{\"arr\":[1,2,3]}}"}</pre>  and use the following mapping template  <pre>#set ($errorMessageObj = $util.parseJson($input.path('$.errorMessage')))<br />{<br />   "errorMessageObjKey2ArrVal" : $errorMessageObj.key2.arr[0]<br />}<br /></pre> You will get the following output: <pre>{<br />   "errorMessageObjKey2ArrVal" : 1<br />}<br /></pre>  | 
| $util\.urlEncode\(\) | Converts a string into "application/x\-www\-form\-urlencoded" format\. | 
| $util\.urlDecode\(\) | Decodes an "application/x\-www\-form\-urlencoded" string\. | 
| $util\.base64Encode\(\) | Encodes the data into a base64\-encoded string\. | 
| $util\.base64Decode\(\) | Decodes the data from a base64\-encoded string\. | 