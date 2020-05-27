# Exporting an HTTP API from API Gateway<a name="http-api-export"></a>

After you've created an HTTP API, you can export an OpenAPI 3\.0 definition of your API from API Gateway\. You can either choose a stage to export, or export the latest configuration of your API\. You can also import an exported API definition into API Gateway to create another, identical API\. To learn more about importing API definitions, see [Importing an HTTP API](http-api-open-api.md#http-api-import)\.

## Export an OpenAPI 3\.0 definition of a stage by using the AWS CLI<a name="http-api-export.stage.example"></a>

The following command exports an OpenAPI definition of an API stage named `prod` to a YAML file named `stage-definition.yaml`\. The exported definition file includes [API Gateway extensions](api-gateway-swagger-extensions.md) by default\.

```
aws apigatewayv2 export-api \
    --api-id api-id  \
    --output-type YAML  \
    --specification OAS30 \
    --stage-name prod \
    stage-definition.yaml
```

## Export an OpenAPI 3\.0 definition of your API's latest changes by using the AWS CLI<a name="http-api-export.latest.example"></a>

The following command exports an OpenAPI definition of an HTTP API to a JSON file named `latest-api-definition.json`\. Because the command doesn't specify a stage, API Gateway exports the latest configuration of your API, whether it has been deployed to a stage or not\. The exported definition file doesn't include [API Gateway extensions](api-gateway-swagger-extensions.md)\.

```
aws apigatewayv2 export-api \
    --api-id api-id  \
    --output-type JSON  \
    --specification OAS30 \
    --no-include-extensions \
    latest-api-definition.json
```

For more information, see [ExportAPI](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-exports-specification.html#apis-apiid-exports-specification-http-methods) in the *Amazon API Gateway Version 2 API Reference*\.