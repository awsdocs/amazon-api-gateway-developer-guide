# Set up gateway response customization in OpenAPI<a name="set-up-gateway-responses-in-swagger"></a>

 You can use the `x-amazon-apigateway-gateway-responses` extension at the API root level to customize gateway responses in OpenAPI\. The following OpenAPI definition shows an example for customizing the [GatewayResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) of the `MISSING_AUTHENTICATION_TOKEN` type\. 

```
  "x-amazon-apigateway-gateway-responses": {
    "MISSING_AUTHENTICATION_TOKEN": {
      "statusCode": 404,
      "responseParameters": {
        "gatewayresponse.header.x-request-path": "method.input.params.petId",
        "gatewayresponse.header.x-request-query": "method.input.params.q",
        "gatewayresponse.header.Access-Control-Allow-Origin": "'a.b.c'",
        "gatewayresponse.header.x-request-header": "method.input.params.Accept"
      },
      "responseTemplates": {
        "application/json": "{\n     \"message\": $context.error.messageString,\n     \"type\":  \"$context.error.responseType\",\n     \"stage\":  \"$context.stage\",\n     \"resourcePath\":  \"$context.resourcePath\",\n     \"stageVariables.a\":  \"$stageVariables.a\",\n     \"statusCode\": \"'404'\"\n}"
      }
    }
```

In this example, the customization changes the status code from the default \(`403`\) to `404`\. It also adds to the gateway response four header parameters and one body mapping template for the `application/json` media type\.