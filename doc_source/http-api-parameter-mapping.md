# Transforming API requests and responses<a name="http-api-parameter-mapping"></a>

You can modify API requests from clients before they reach your backend integrations\. You can also change the response from integrations before API Gateway returns the response to clients\. You use *parameter mapping* to modify API requests and responses for HTTP APIs\. To use parameter mapping, you specify API request or response parameters to modify, and specify how to modify those parameters\.



## Transforming API requests<a name="http-api-mapping-request-parameters"></a>

You use request parameters to change requests before they reach your backend integrations\. You can modify headers, query strings, the request path, or the request body\.

Request parameters are a key\-value map\. The key identifies the location of the request parameter to change, and how to change it\. The value specifies the new data for the parameter\.

The following table shows supported keys\.


**Parameter mapping keys**  

| Type | Syntax | 
| --- | --- | 
| Header | append\|overwrite\|remove:header\.headername | 
| Query string | append\|overwrite\|remove:querystring\.querystring\-name | 
| Path | overwrite:path | 

The following table shows supported values that you can map to parameters\.


**Request parameter mapping values**  

| Type | Syntax | Notes | 
| --- | --- | --- | 
| Header value | $request\.header\.name | Header names are case\-insensitive\. API Gateway combines multiple header values with commas, for example "header1": "value1,value2"\. Some headers are reserved\. To learn more, see [Reserved headers](#http-api-mapping-reserved-headers)\. | 
| Query string value | $request\.querystring\.name | Query string names are case\-sensitive\. API Gateway combines multiple values with commas, for example "querystring1" "Value1,Value2"\. | 
| Request body | $request\.body\.name | A JSON path expression\. Recursive descent \($request\.body\.\.name\) and filter expressions \(?\(expression\)\) aren't supported\.  When you specify a JSON path, API Gateway truncates the request body at 100 KB and then applies the selection expression\. To send payloads larger than 100 KB, specify `$request.body`\.   | 
| Request path | $request\.path | The request path, without the stage name\. | 
| Path parameter | $request\.path\.name | The value of a path parameter in the request\. For example if the route is /pets/\{petId\}, you can map the petId parameter from the request with $request\.path\.petId\. | 
| Context variable | $context\.variableName | The value of a [context variable](http-api-logging-variables.md)\. | 
| Stage variable | $stageVariables\.variableName | The value of a [stage variable](http-api-stages.md#http-api-stages.stage-variables)\. | 
| Static value | string | A constant value\. | 

## Transforming API responses<a name="http-api-mapping-request-parameters"></a>

You use response parameters to transform the HTTP response from a backend integration before returning the response to clients\. You can modify headers or the status code of a response before API Gateway returns the response to clients\.

You configure response parameters for each status code that your integration returns\. Response parameters are a key\-value map\. The key identifies the location of the request parameter to change, and how to change it\. The value specifies the new data for the parameter\.

The following table shows supported keys\.


**Response parameter mapping keys**  

| Type | Syntax | 
| --- | --- | 
| Header | append\|overwrite\|remove:header\.headername | 
| Status code | overwrite:statuscode | 

The following table shows supported values that you can map to parameters\.


**Response parameter mapping values**  

| Type | Syntax | Notes | 
| --- | --- | --- | 
| Header value | $request\.header\.name | Header names are case\-insensitive\. API Gateway combines multiple header values with commas, for example "header1": "value1,value2"\. Some headers are reserved\. To learn more, see [Reserved headers](#http-api-mapping-reserved-headers)\. | 
| Response body | $response\.body\.name | A JSON path expression\. Recursive descent \($response\.body\.\.name\) and filter expressions \(?\(expression\)\) aren't supported\.  When you specify a JSON path, API Gateway truncates the response body at 100 KB and then applies the selection expression\. To send payloads larger than 100 KB, specify `$response.body`\.   | 
| Context variable | $context\.variableName | The value of a supported [context variable](http-api-logging-variables.md)\. | 
| Stage variable | $stageVariables\.variableName | The value of a [stage variable](http-api-stages.md#http-api-stages.stage-variables)\. | 
| Static value | string | A constant value\. | 

## Reserved headers<a name="http-api-mapping-reserved-headers"></a>

The following headers are reserved\. You can't configure request or response mappings for these headers\.
+ access\-control\-\*
+ apigw\-\*
+ Authorization
+ Connection
+ Content\-Encoding
+ Content\-Length
+ Content\-Location
+ Forwarded
+ Keep\-Alive
+ Origin
+ Proxy\-Authenticate
+ Proxy\-Authorization
+ TE
+ Trailers 
+ Transfer\-Encoding
+ Upgrade
+ x\-amz\-\*
+ x\-amzn\-\*
+ X\-Forwarded\-For
+ X\-Forwarded\-Host
+ X\-Forwarded\-Proto
+ Via

## Examples<a name="http-api-parameter-mapping-examples"></a>

The following AWS CLI examples configure parameter mappings\. For example AWS CloudFormation templates, see [GitHub](https://github.com/awsdocs/amazon-api-gateway-developer-guide/tree/main/cloudformation-templates)\.

### Add a header to an API request<a name="http-api-parameter-mapping-examples-request-header"></a>

The following example adds a header named `header1` to an API request before it reaches your backend integration\. API Gateway populates the header with the request ID\.

```
aws apigatewayv2 create-integration \
    --api-id abcdef123 \
    --integration-type HTTP_PROXY \
    --payload-format-version 1.0 \
    --integration-uri 'https://api.example.com' \
    --integration-method ANY \
    --request-parameters '{ "append:header.header1": "$context.requestId" }'
```

### Rename a request header<a name="http-api-parameter-mapping-examples-response"></a>

The following example renames a request header from `header1` to `header2`\.

```
aws apigatewayv2 create-integration \
    --api-id abcdef123 \
    --integration-type HTTP_PROXY \
    --payload-format-version 1.0 \
    --integration-uri 'https://api.example.com' \
    --integration-method ANY \
    --request-parameters '{ "append:header.header2": "$request.header.header1",  "remove:header.header1": "''"}'  }
```

### Change the response from an integration<a name="http-api-parameter-mapping-examples-response"></a>

The following example configures response parameters for an integration\. When the integrations returns a 500 status code, API Gateway changes the status code to 403, and adds `header1`1 to the response\. When the integration returns a 404 status code, API Gateway adds an `error` header to the response\.

```
aws apigatewayv2 create-integration \
    --api-id abcdef123 \
    --integration-type HTTP_PROXY \
    --payload-format-version 1.0 \
    --integration-uri 'https://api.example.com' \
    --integration-method ANY \
    --response-parameters '{"500" : {"append:header.header1": "$context.requestId", "overwrite:statuscode" : "403"}, "404" : {"append:header.error" : "$stageVariables.environmentId"}  }
```

### Remove configured parameter mappings<a name="http-api-parameter-mapping-examples-remove"></a>

The following example command removes previously configured request parameters for `append:header.header1`\. It also removes previously configured response parameters for a 200 status code\.

```
aws apigatewayv2 update-integration \
    --api-id abcdef123 \
    --integration-type HTTP_PROXY \
    --payload-format-version 1.0 \
    --integration-uri 'https://api.example.com' \
    --integration-method ANY \
    --request-parameters {"append:header.header1" : ""} \ 
    --response-parameters {"200" : {}}
```