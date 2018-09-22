# x\-amazon\-apigateway\-gateway\-responses\.responseTemplates Object<a name="api-gateway-swagger-extensions-gateway-responses.responseTemplates"></a>

Defines [GatewayResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) mapping templates, as a string\-to\-string map of key\-value pairs, for a given gateway response\. For each key\-value pair, the key is the content type; for example, "application/json", and the value is a stringified mapping template for simple variable substitutions\. A `GatewayResponse` mapping template is not processed by the [Velocity Template Language \(VTL\)](http://velocity.apache.org/engine/devel/vtl-reference-guide.html) engine\.


**Properties**  

| Property Name | Type | Description | 
| --- | --- | --- | 
| content\-type | string |  A `GatewayResponse` body mapping template supporting only simple variable substitution to customize a gateway response body\.  | 

## x\-amazon\-apigateway\-gateway\-responses\.responseTemplates Example<a name="api-gateway-swagger-extensions-gateway-responses.responseTemplates-example"></a>

 The following Swagger extensions example shows a [GatewayResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) mapping template to customize an API Gateway\-generated error response into an app\-specific format\. 

```
      "responseTemplates": {
        "application/json": "{ \"message\": $context.error.messageString, \"type\":$context.error.responseType, \"statusCode\": '488' }
      }
```

 The following Swagger extensions example shows a [GatewayResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) mapping template to override an API Gateway\-generated error response with a static error message\. 

```
      "responseTemplates": {
        "application/json": "{ \"message\": 'API-specific errors' }" }
      }
```