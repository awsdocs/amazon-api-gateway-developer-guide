# x\-amazon\-apigateway\-integration\.requestParameters object<a name="api-gateway-swagger-extensions-integration-requestParameters"></a>

For REST APIs, specifies mappings from named method request parameters to integration request parameters\. The method request parameters must be defined before being referenced\. 

For HTTP APIs, specifies parameters that are passed to `AWS_PROXY` integrations with a specified `integrationSubtype`\. 


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| integration\.request\.<param\-type>\.<param\-name> | string |  For REST APIs, the value is typically a predefined method request parameter of the `method.request.<param-type>.<param-name>` format, where `<param-type>` can be `querystring`, `path`, `header`, or `body`\. However, `$context.VARIABLE_NAME`, `$stageVariables.VARIABLE_NAME`, and `STATIC_VALUE` are also valid\. For the `body` parameter, the `<param-name>` is a JSON path expression without the `$.` prefix\.   | 
| parameter | string |  For HTTP APIs, request parameters are a key\-value map specifying parameters that are passed to `AWS_PROXY` integrations with a specified `integrationSubtype`\. You can provide static values, or map request data, stage variables, or context variables that are evaluated at runtime\. To learn more, see [Working with AWS service integrations for HTTP APIs](http-api-develop-integrations-aws-services.md)\.  | 

## `x-amazon-apigateway-integration.requestParameters` example<a name="api-gateway-swagger-extensions-request-parameters-example"></a>

The following request parameter mappings example translates a method request's query \(`version`\), header \(`x-user-id`\), and path \(`service`\) parameters to the integration request's query \(`stage`\), header \(`x-userid`\), and path parameters \(`op`\), respectively\.

**Note**  
If you're creating resources through OpenAPI or AWS CloudFormation, static values should be enclosed in single quotes\.  
To add this value from the console, enter `application/json` in the box, without quotation marks\.

```
"requestParameters" : {
    "integration.request.querystring.stage" : "method.request.querystring.version",
    "integration.request.header.x-userid" : "method.request.header.x-user-id",
    "integration.request.path.op" : "method.request.path.service"
},
```