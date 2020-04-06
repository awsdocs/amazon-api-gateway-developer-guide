# x\-amazon\-apigateway\-integration\.responseTemplates object<a name="api-gateway-swagger-extensions-integration-responseTemplates"></a>

 Specifies mapping templates for a response payload of the specified MIME types\. 


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| MIME type | string |  Specifies a mapping template to transform the integration response body to the method response body for a given MIME type\. For information about creating a mapping template, see [Mapping templates](rest-api-data-transformations.md#models-mappings-mappings)\. An example of the *MIME type* is `application/json`\.   | 

## x\-amazon\-apigateway\-integration\.responseTemplate example<a name="api-gateway-swagger-extensions-response-template-example"></a>

 The following example sets mapping templates for a request payload of the `application/json` and `application/xml` MIME types\. 

```
"responseTemplates" : {
    "application/json" : "#set ($root=$input.path('$')) { \"stage\": \"$root.name\", \"user-id\": \"$root.key\" }",
    "application/xml" : "#set ($root=$input.path('$')) <stage>$root.name</stage> "
}
```