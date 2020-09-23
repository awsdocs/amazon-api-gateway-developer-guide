# x\-amazon\-apigateway\-gateway\-responses\.responseParameters object<a name="api-gateway-swagger-extensions-gateway-responses.responseParameters"></a>

Defines a string\-to\-string map of key\-value pairs to generate gateway response parameters from the incoming request parameters or using literal strings\. Supported only for REST APIs\.


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| gatewayresponse\.param\-position\.param\-name | string |  `param-position` can be `header`, `path`, or `querystring`\. For more information, see [Map method request data to integration request parametersMap route request data to integration request parameters](request-response-data-mappings.md#mapping-request-parameters)\.  | 

## x\-amazon\-apigateway\-gateway\-responses\.repsonseParameters example<a name="api-gateway-swagger-extensions-gateway-responses.repsonseParameters-example"></a>

 The following OpenAPI extensions example shows a [GatewayResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) response parameter mapping expression to enable CORS support for resources on the `*.example.domain` domains\. 

```
      "responseParameters": {
        "gatewayresponse.header.Access-Control-Allow-Origin": '*.example.domain',
        "gatewayresponse.header.from-request-header" : method.request.header.Accept,
        "gatewayresponse.header.from-request-path" : method.request.path.petId,
        "gatewayresponse.header.from-request-query" : method.request.querystring.qname
      }
```