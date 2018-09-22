# x\-amazon\-apigateway\-any\-method Object<a name="api-gateway-swagger-extensions-any-method"></a>

 Specifies the [Swagger Operation Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#operationObject) for the API Gateway catch\-all `ANY` method in a [Swagger Path Item Object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#pathItemObject)\. This object can exist alongside other Operation objects and will catch any HTTP method that was not explicitly declared\. 

 The following table lists the properties extended by API Gateway\. For the other Swagger Operation properties, see the Swagger specification\. 


**Properties**  

| Property Name | Type | Description | 
| --- | --- | --- | 
| x\-amazon\-apigateway\-integration | [x\-amazon\-apigateway\-integration Object](api-gateway-swagger-extensions-integration.md) |  Specifies the integration of the method with the backend\. This is an extended property of the [Swagger Operation](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#operationObject) object\. The integration can be of type AWS, AWS\_PROXY, HTTP, HTTP\_PROXY, or MOCK\.  | 

## x\-amazon\-apigateway\-any\-method Example<a name="api-gateway-swagger-extensions-any-method-example"></a>

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