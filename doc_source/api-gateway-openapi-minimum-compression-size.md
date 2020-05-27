# x\-amazon\-apigateway\-minimum\-compression\-size<a name="api-gateway-openapi-minimum-compression-size"></a>

Specifies the minimum compression size for a REST API\. To enable compression, specify an integer between 0 and 10485760\. To learn more, see [Enabling payload compression for an API](api-gateway-gzip-compression-decompression.md)\.

## x\-amazon\-apigateway\-minimum\-compression\-size example<a name="api-gateway-openapi-minimum-compression-size-example"></a>

The following example specifies a minimum compression size of `5242880` bytes for a REST API\.

```
"x-amazon-apigateway-minimum-compression-size": 5242880
```