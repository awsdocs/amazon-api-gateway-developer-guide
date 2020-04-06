# x\-amazon\-apigateway\-integration\.requestTemplates object<a name="api-gateway-swagger-extensions-integration-requestTemplates"></a>

 Specifies mapping templates for a request payload of the specified MIME types\. 


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| MIME type | string |   An example of the MIME type is `application/json`\. For information about creating a mapping template, see [Mapping templates](rest-api-data-transformations.md#models-mappings-mappings)\.   | 

## x\-amazon\-apigateway\-integration\.requestTemplates example<a name="api-gateway-swagger-extensions-request-template-example"></a>

 The following example sets mapping templates for a request payload of the `application/json` and `application/xml` MIME types\. 

```
"requestTemplates" : {
    "application/json" : "#set ($root=$input.path('$')) { \"stage\": \"$root.name\", \"user-id\": \"$root.key\" }",
    "application/xml" : "#set ($root=$input.path('$')) <stage>$root.name</stage> "
}
```