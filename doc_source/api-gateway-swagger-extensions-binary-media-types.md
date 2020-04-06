# x\-amazon\-apigateway\-binary\-media\-types property<a name="api-gateway-swagger-extensions-binary-media-types"></a>

Specifies the list of binary media types to be supported by API Gateway, such as `application/octet-stream` and `image/jpeg`\. This extension is a JSON array\. It should be included as a top\-level vendor extension to the OpenAPI document\.

## x\-amazon\-apigateway\-binary\-media\-types example<a name="api-gateway-swagger-extensions-binary-media-types-example"></a>

The following example shows the encoding lookup order of an API\.

```
"x-amazon-apigateway-binary-media-types: [ "application/octet", "image/jpeg" ]
```