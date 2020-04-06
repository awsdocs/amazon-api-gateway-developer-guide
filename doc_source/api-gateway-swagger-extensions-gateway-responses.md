# x\-amazon\-apigateway\-gateway\-responses object<a name="api-gateway-swagger-extensions-gateway-responses"></a>

Defines the gateway responses for an API as a string\-to\-[GatewayResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) map of key\-value pairs\.


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| responseType | [x\-amazon\-apigateway\-gateway\-responses\.gatewayResponse](api-gateway-swagger-extensions-gateway-responses.gatewayResponse.md) |  A `GatewayResponse` for the specified *responseType*\.  | 

## x\-amazon\-apigateway\-gateway\-responses example<a name="api-gateway-swagger-extensions-gateway-responses-example"></a>

 The following API Gateway extension to OpenAPI example defines a [GatewayResponses](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-responses/) map that contains two [GatewayResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) instances—one for the `DEFAULT_4XX` type and another for the `INVALID_API_KEY` type\. 

```
{
  "x-amazon-apigateway-gateway-responses": {
    "DEFAULT_4XX": {
      "responseParameters": {
        "gatewayresponse.header.Access-Control-Allow-Origin": "'domain.com'"
      },
      "responseTemplates": {
        "application/json": "{\"message\": test 4xx b }"
      }
    },
    "INVALID_API_KEY": {
      "statusCode": "429",
      "responseTemplates": {
        "application/json": "{\"message\": test forbidden }"
      }
    }
  }
}
```