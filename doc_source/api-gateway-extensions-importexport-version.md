# x\-amazon\-apigateway\-importexport\-version<a name="api-gateway-extensions-importexport-version"></a>

Specifies the version of the API Gateway import and export algorithm for HTTP APIs\. Currently, the only supported value is `1.0`\. To learn more, see [exportVersion](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-exports-specification.html#w125aab9c10b3b1b4) in the *API Gateway Version 2 API Reference*\.

## x\-amazon\-apigateway\-importexport\-version example<a name="api-gateway-extensions-importexport-version-example"></a>

The following example sets the import and export version to `1.0`\.

```
{
    "openapi": "3.0.1",
    "x-amazon-apigateway-importexport-version": "1.0",
    "info": { ...
```