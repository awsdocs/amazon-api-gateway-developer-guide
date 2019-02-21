# x\-amazon\-apigateway\-integration\.requestParameters Object<a name="api-gateway-swagger-extensions-integration-requestParameters"></a>

 Specifies mappings from named method request parameters to integration request parameters\. The method request parameters must be defined before being referenced\. 


**Properties**  

| Property Name | Type | Description | 
| --- | --- | --- | 
| integration\.request\.<param\-type>\.<param\-name> | string |  The value must be a predefined method request parameter of the `method.request.<param-type>.<param-name>` format, where `<param-type>` can be `querystring`, `path`, `header`, or `body`\. For the `body` parameter, the `<param-name>` is a JSON path expression without the `$.` prefix\.   | 

## `x-amazon-apigateway-integration.requestParameters` Example<a name="api-gateway-swagger-extensions-request-parameters-example"></a>

The following request parameter mappings example translates a method request's query \(`version`\), header \(`x-user-id`\) and path \(`service`\) parameters to the integration request's query \(`stage`\), header \(`x-userid`\), and path parameters \(`op`\), respectively\.

**Note**  
If you are creating resources via OpenAPI or AWS CloudFormation, static values should be enclosed in single quotes\.  
To add this value from the console, enter `application/json` in the box, without quotation marks\.

```
"requestParameters" : {
    "integration.request.querystring.stage" : "method.request.querystring.version",
    "integration.request.header.x-userid" : "method.request.header.x-user-id",
    "integration.request.path.op" : "method.request.path.service"
},
```