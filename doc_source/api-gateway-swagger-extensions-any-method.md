# x\-amazon\-apigateway\-any\-method object<a name="api-gateway-swagger-extensions-any-method"></a>

 Specifies the [OpenAPI Operation Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#operationObject) for the API Gateway catch\-all `ANY` method in an [OpenAPI Path Item Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#pathItemObject)\. This object can exist alongside other Operation objects and will catch any HTTP method that wasn't explicitly declared\. 

 The following table lists the properties extended by API Gateway\. For the other OpenAPI Operation properties, see the OpenAPI specification\. 


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| isDefaultRoute | Boolean | Specifies whether a route is the $default route\. Supported only for HTTP APIs\. To learn more, see [Working with routes for HTTP APIs](http-api-develop-routes.md)\. | 
| x\-amazon\-apigateway\-integration | [x\-amazon\-apigateway\-integration object](api-gateway-swagger-extensions-integration.md) |  Specifies the integration of the method with the backend\. This is an extended property of the [OpenAPI Operation](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#operationObject) object\. The integration can be of type AWS, AWS\_PROXY, HTTP, HTTP\_PROXY, or MOCK\.  | 

## x\-amazon\-apigateway\-any\-method examples<a name="api-gateway-swagger-extensions-any-method-example"></a>

The following example integrates the `ANY` method on a proxy resource, `{proxy+}`, with a Lambda function, `TestSimpleProxy`\.

```
    "/{proxy+}": {
      "x-amazon-apigateway-any-method": {
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "proxy",
            "in": "path",
            "required": true,
            "type": "string"
          }
        ],
        "responses": {},
        "x-amazon-apigateway-integration": {
          "uri": "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:TestSimpleProxy/invocations",
          "passthroughBehavior": "when_no_match",
          "httpMethod": "POST",
          "type": "aws_proxy"
        }
```

The following example creates a `$default` route for an HTTP API that integrates with a Lambda function, `HelloWorld`\.

```
"/$default": {
    "x-amazon-apigateway-any-method": {
      "isDefaultRoute": true,
      "x-amazon-apigateway-integration": {
        "type": "AWS_PROXY",
        "httpMethod": "POST",
        "uri": "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:HelloWorld/invocations",
        "timeoutInMillis": 1000,
        "connectionType": "INTERNET",
        "payloadFormatVersion": 1.0
      }
   }
}
```