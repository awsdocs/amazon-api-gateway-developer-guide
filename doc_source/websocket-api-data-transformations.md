# Setting up data transformations for WebSocket APIs<a name="websocket-api-data-transformations"></a>

In API Gateway, a WebSocket API's method request can take a payload in a different format from the corresponding integration request payload, as required in the backend\. Similarly, the backend may return an integration response payload different from the method response payload, as expected by the frontend\. 

API Gateway lets you use mapping templates to map the payload from a method request to the corresponding integration request and from an integration response to the corresponding method response\. You specify a template selection expression to determine which template to use to perform the necessary data transformations\.

You can use data mappings to map data from a [route request](api-gateway-basic-concept.md#apigateway-definition-route-request) to a backend integration\. To learn more, see [Setting up data mapping for WebSocket APIs](websocket-api-data-mapping.md)\.

## Mapping templates and models<a name="apigateway-websocket-api-mapping-templats-and-models"></a>

 A *mapping template* is a script expressed in [Velocity Template Language \(VTL\)](https://velocity.apache.org/engine/devel/vtl-reference.html) and applied to the payload using [JSONPath expressions](http://goessner.net/articles/JsonPath/)\. For more information about API Gateway mapping templates, see [Mapping templates](rest-api-data-transformations.md#models-mappings-mappings)\.

The payload can have a *data model* according to the [JSON schema draft 4](https://tools.ietf.org/html/draft-zyp-json-schema-04)\. You do not have to define a model to create a mapping template\. However, a model can help you create a template because API Gateway generates a template blueprint based on a provided model\. For more information about API Gateway models, see [Models](rest-api-data-transformations.md#models-mappings-models)\.

## Template selection expressions<a name="apigateway-websocket-api-template-selection-expressions"></a>

To transform a payload with a mapping template, you specify a WebSocket API template selection expression in an [integration request](apigateway-websocket-api-integration-requests.md) or [integration response](apigateway-websocket-api-integration-responses.md)\. This expression is evaluated to determine the input or output template \(if any\) to use to transform either the request body into the integration request body \(via an input template\) or the integration response body to the route response body \(via an output template\)\.

`Integration.TemplateSelectionExpression` supports `${request.body.jsonPath}` and static values\.

`IntegrationResponse.TemplateSelectionExpression` supports `${request.body.jsonPath}`, `${integration.response.statuscode}`, `${integration.response.header.headerName}`, `${integration.response.multivalueheader.headerName}`, and static values\.

## Integration response selection expressions<a name="apigateway-websocket-api-integration-response-selection-expressions"></a>

When you [set up an integration response](apigateway-websocket-api-integration-responses.md) for a WebSocket API, you can optionally specify an integration response selection expression\. This expression determines what `[https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid-integrationresponses-integrationresponseid.html](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid-integrationresponses-integrationresponseid.html)` should be selected when an integration returns\. The value of this expression is currently restricted by API Gateway, as defined below\. Realize that this expression is only relevant for *non\-proxy integrations*; a proxy integration simply passes the response payload back to the caller without modeling or modification\.

Unlike the other preceding selection expressions, this expression currently supports a *pattern\-matching* format\. The expression should be wrapped with forward slashes\.

Currently the value is fixed depending on the `[https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid.html#apis-apiid-integrations-integrationid-prop-integration-integrationtype](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid.html#apis-apiid-integrations-integrationid-prop-integration-integrationtype)`:
+ For Lambda\-based integrations, it is `$integration.response.body.errorMessage`\.
+ For `HTTP` and `MOCK` integrations, it is `$integration.response.statuscode`\.
+ For `HTTP_PROXY` and `AWS_PROXY`, the expression isn't utilized because you're requesting that the payload pass through to the caller\.