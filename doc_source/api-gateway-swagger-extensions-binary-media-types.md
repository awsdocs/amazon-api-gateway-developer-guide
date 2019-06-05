# x\-amazon\-apigateway\-binary\-media\-types Property<a name="api-gateway-swagger-extensions-binary-media-types"></a>

Specifies the list of binary media types to be supported by API Gateway, such as `application/octet-stream`, `image/jpeg`, etc\. This extension is a JSON Array\. It should be included as a top\-level vendor extension to the OpenAPI document\.

## x\-amazon\-apigateway\-binary\-media\-types Example<a name="api-gateway-swagger-extensions-binary-media-types-example"></a>

The following example shows the encoding lookup order of an API\.

```
"x-amazon-apigateway-binary-media-types: [ "application/octet", "image/jpeg" ]
```