# x\-amazon\-apigateway\-integration\.responseParameters object<a name="api-gateway-swagger-extensions-integration-responseParameters"></a>

 Specifies mappings from integration method response parameters to method response parameters\. Only the `header` and `body` types of the integration response parameters can be mapped to the `header` type of the method response\. Supported only for REST APIs\.


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| method\.response\.header\.<param\-name> | string |   The named parameter value can be derived from the `header` and `body` types of the integration response parameters only\.   | 

## `x-amazon-apigateway-integration.responseParameters` example<a name="api-gateway-swagger-extensions-response-parameters-example"></a>

The following example maps `body` and `header` parameters of the integration response to two `header` parameters of the method response\. 

```
"responseParameters" : {
    "method.response.header.Location" : "integration.response.body.redirect.url",
    "method.response.header.x-user-id" : "integration.response.header.x-userid"
}
```