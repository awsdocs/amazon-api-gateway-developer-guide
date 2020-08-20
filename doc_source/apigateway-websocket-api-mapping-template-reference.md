# API Gateway WebSocket API mapping template reference<a name="apigateway-websocket-api-mapping-template-reference"></a>

This section summarizes the set of variables that are currently supported for WebSocket APIs in API Gateway\.


| Parameter | Description | 
| --- | --- | 
| $context\.connectionId |  A unique ID for the connection that can be used to make a callback to the client\.  | 
| $context\.connectedAt |  The [Epoch](https://en.wikipedia.org/wiki/Unix_time)\-formatted connection time\.  | 
| $context\.domainName |  A domain name for the WebSocket API\. This can be used to make a callback to the client \(instead of a hard\-coded value\)\.  | 
| $context\.eventType |  The event type: `CONNECT`, `MESSAGE`, or `DISCONNECT`\.  | 
| $context\.messageId |  A unique server\-side ID for a message\. Available only when the `$context.eventType` is `MESSAGE`\.  | 
| $context\.routeKey |  The selected route key\.  | 
| $context\.requestId |  Same as `$context.extendedRequestId`\.  | 
| $context\.extendedRequestId | An automatically generated ID for the API call, which contains more useful information for debugging/troubleshooting\. | 
| $context\.apiId |  The identifier API Gateway assigns to your API\.  | 
| $context\.authorizer\.principalId |  The principal user identification associated with the token sent by the client and returned from an API Gateway Lambda authorizer \(formerly known as a custom authorizer\) Lambda function\.  | 
| $context\.authorizer\.property |  The stringified value of the specified key\-value pair of the `context` map returned from an API Gateway Lambda authorizer function\. For example, if the authorizer returns the following `context` map:  <pre>"context" : {<br />  "key": "value",<br />  "numKey": 1,<br />  "boolKey": true<br />}</pre> calling `$context.authorizer.key` returns the `"value"` string, calling `$context.authorizer.numKey` returns the `"1"` string, and calling `$context.authorizer.boolKey` returns the `"true"` string\.  | 
| $context\.error\.messageString | The quoted value of $context\.error\.message, namely "$context\.error\.message"\. | 
| $context\.error\.validationErrorString |  A string containing a detailed validation error message\.  | 
| $context\.identity\.accountId |  The AWS account ID associated with the request\.  | 
| $context\.identity\.apiKey |  The API owner key associated with key\-enabled API request\.  | 
| $context\.identity\.apiKeyId | The API key ID associated with the key\-enabled API request | 
| $context\.identity\.caller |  The principal identifier of the caller making the request\.  | 
| $context\.identity\.cognitoAuthenticationProvider |  A comma\-separated list of the Amazon Cognito authentication providers used by the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\. For information related to this and the other Amazon Cognito `$context` variables, see [Using Federated Identities](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html) in the *Amazon Cognito Developer Guide*\.  | 
| $context\.identity\.sourceIp |  The source IP address of the TCP connection making the request to API Gateway\.  | 
| $context\.identity\.user |  The principal identifier of the user making the request\.  | 
| $context\.identity\.userAgent |  The User Agent of the API caller\.  | 
| $context\.identity\.userArn |  The Amazon Resource Name \(ARN\) of the effective user identified after authentication\.  | 
| $context\.requestTime | The [CLF](https://httpd.apache.org/docs/1.3/logs.html#common)\-formatted request time \(dd/MMM/yyyy:HH:mm:ss \+\-hhmm\)\. | 
| $context\.requestTimeEpoch | The [Epoch](https://en.wikipedia.org/wiki/Unix_time)\-formatted request time\. | 
| $context\.stage |  The deployment stage of the API call \(for example, Beta or Prod\)\.  | 
| $context\.status |  The response status\.  | 
| $input\.body | Returns the raw payload as a string\. | 
| $input\.json\(x\) | This function evaluates a JSONPath expression and returns the results as a JSON string\. For example, `$input.json('$.pets')` will return a JSON string representing the pets structure\. For more information about JSONPath, see [JSONPath](http://goessner.net/articles/JsonPath/) or [JSONPath for Java](https://github.com/jayway/JsonPath)\. | 
| $input\.path\(x\) | Takes a JSONPath expression string \(`x`\) and returns a JSON object representation of the result\. This allows you to access and manipulate elements of the payload natively in [Apache Velocity Template Language \(VTL\)](https://velocity.apache.org/engine/devel/vtl-reference.html)\. For example, if the expression `$input.path('$.pets')` returns an object like this: <pre>[<br />  { <br />    "id": 1, <br />    "type": "dog", <br />    "price": 249.99 <br />  }, <br />  { <br />    "id": 2, <br />    "type": "cat", <br />    "price": 124.99 <br />  }, <br />  { <br />    "id": 3, <br />    "type": "fish", <br />    "price": 0.99 <br />  } <br />]</pre> `$input.path('$.pets').count()` would return `"3"`\. For more information about JSONPath, see [JSONPath](http://goessner.net/articles/JsonPath/) or [JSONPath for Java](https://github.com/jayway/JsonPath)\. | 
| $stageVariables\.<variable\_name> |  *<variable\_name>* represents a stage variable name\.  | 
| $stageVariables\['<variable\_name>'\] |  *<variable\_name>* represents any stage variable name\.  | 
| $\{stageVariables\['<variable\_name>'\]\} |  *<variable\_name>* represents any stage variable name\.  | 
| $util\.escapeJavaScript\(\) |  Escapes the characters in a string using JavaScript string rules\.  This function will turn any regular single quotes \(`'`\) into escaped ones \(`\'`\)\. However, the escaped single quotes are not valid in JSON\. Thus, when the output from this function is used in a JSON property, you must turn any escaped single quotes \(`\'`\) back to regular single quotes \(`'`\)\. This is shown in the following example:  <pre> $util.escapeJavaScript(data).replaceAll("\\'","'")</pre>   | 
| $util\.parseJson\(\) |   Takes "stringified" JSON and returns an object representation of the result\. You can use the result from this function to access and manipulate elements of the payload natively in Apache Velocity Template Language \(VTL\)\. For example, if you have the following payload:  <pre>{"errorMessage":"{\"key1\":\"var1\",\"key2\":{\"arr\":[1,2,3]}}"}</pre>  and use the following mapping template  <pre>#set ($errorMessageObj = $util.parseJson($input.path('$.errorMessage')))<br />{<br />   "errorMessageObjKey2ArrVal" : $errorMessageObj.key2.arr[0]<br />}<br /></pre> You will get the following output: <pre>{<br />   "errorMessageObjKey2ArrVal" : 1<br />}<br /></pre>  | 
| $util\.urlEncode\(\) | Converts a string into "application/x\-www\-form\-urlencoded" format\. | 
| $util\.urlDecode\(\) | Decodes an "application/x\-www\-form\-urlencoded" string\. | 
| $util\.base64Encode\(\) | Encodes the data into a base64\-encoded string\. | 
| $util\.base64Decode\(\) | Decodes the data from a base64\-encoded string\. | 