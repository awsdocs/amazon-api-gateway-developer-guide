# WebSocket Selection Expressions in API Gateway<a name="apigateway-websocket-api-selection-expressions"></a>

**Topics**
+ [Route Selection Expressions](#apigateway-websocket-api-route-selection-expressions)
+ [Model Selection Expressions](#apigateway-websocket-api-model-selection-expressions)
+ [Template Selection Expressions](#apigateway-websocket-api-template-selection-expressions)
+ [Route Response Selection Expressions](#apigateway-websocket-api-route-response-selection-expressions)
+ [API Key Selection Expressions](#apigateway-websocket-api-apikey-selection-expressions)
+ [API Mapping Selection Expressions](#apigateway-websocket-api-mapping-selection-expressions)
+ [Integration Response Selection Expressions](#apigateway-websocket-api-integration-response-selection-expressions)
+ [WebSocket Selection Expression Summary](#apigateway-websocket-api-selection-expression-table)

API Gateway uses selection expressions as a way to evaluate request and response context and produce a key\. The key is then used to select from a set of possible values, typically provided by you, the API developer\. The exact set of supported variables will vary depending on the particular expression\. Each expression is discussed in more detail below\.

For all of the expressions, the language follows the same set of rules:
+ A variable is prefixed with `"$"`\.
+ Curly braces can be used to explicitly define variable boundaries, e\.g\., `"${request.body.version}-beta"`\.
+ Multiple variables are supported, but evaluation occurs only once \(no recursive evaluation\)\.
+ A dollar sign \(`$`\) can be escaped with `"\"`\. This is most useful when defining an expression that maps to the reserved `$default` key, e\.g\., `"\$default"`\.
+ In some cases, a pattern format is required\. In this case, the expression should be wrapped with forward slashes \(`"/"`\), e\.g\. `"/2\d\d/"` to match `2XX` status codes\.

## Route Selection Expressions<a name="apigateway-websocket-api-route-selection-expressions"></a>

A *route selection expression* is evaluated when the service is selecting the route to follow for an incoming message\. The service will use the route whose `routeKey` exactly matches the evaluated value\. If none match, and a route with the `$default` route key exists, that will be selected\. If no routes match the evaluated value and there is no `$default` route, the service will return an error\. For WebSocket\-based APIs, the expression should be of the form `$request.body.{path_to_body_element}`\.

For example, suppose you are sending the following JSON message:

```
{
    "service" : "chat",
    "action" : "join",
    "data" : {
        "room" : "room1234"
   }
}
```

You might want to select your API's behavior based on the `action` property\. In that case, you might define the following route selection expression:

```
$request.body.action
```

In this example, `request.body` refers to your message's JSON payload, and `.action` is a [JSONPath](https://goessner.net/articles/JsonPath/) expression\. You can use any JSON path expression after `request.body`, but keep in mind that the result will be stringified\. For example, if your JSONPath expression returns an array of two elements, that will be presented as the string `"[item1, item2]"`\. For this reason, it is good practice to have your expression evaluate to a value and not an array or an object\.

You can simply use a static value, or you can use multiple variables\. The following table shows examples and their evaluated results against the above payload\.


| Expression | Evaluated Result | Description | 
| --- | --- | --- | 
| $request\.body\.action | join | An unwrapped variable | 
| $\{request\.body\.action\} | join | A wrapped variable | 
| $\{request\.body\.service\}/$\{request\.body\.action\} | chat/join | Multiple variables with static values | 
| $\{request\.body\.action\}\-$\{request\.body\.invalidPath\}  | join- | If the JSONPath is not found, the variable will be resolved as ""\. | 
| action | action | Static value | 
| \\$default | $default | Static value | 

The evaluated result will be used to find a route\. If there is a route with a matching route key, the route will be selected to process the message\. If no matching route is found, then API Gateway will try to find the `$default` route if available\. If the `$default` route is not defined, then API Gateway will return an error\.

## Model Selection Expressions<a name="apigateway-websocket-api-model-selection-expressions"></a>

When you define a [route](apigateway-websocket-api-route-route-request.md) for a WebSocket API, you can optionally specify a *model selection expression*\. This expression is evaluated to select the model to be used for body validation when a request is received\. The expression evaluates to one of the entries in a route's [https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-routes.html#apis-apiid-routes-prop-route-requestmodels](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-routes.html#apis-apiid-routes-prop-route-requestmodels)\.

A model is expressed as a [JSON schema](https://tools.ietf.org/html/draft-zyp-json-schema-04) and describes the data structure of the request body\. The nature of this selection expressions allows you to dynamically choose the model to validate against at runtime for a particular route\. For information about how to create a model, see [Create Models and Mapping Templates for Request and Response Mappings](models-mappings.md)\. 

## Template Selection Expressions<a name="apigateway-websocket-api-template-selection-expressions"></a>

When you define an [integration request](apigateway-websocket-api-integration-requests.md) or [integration response](apigateway-websocket-api-integration-responses.md) for a WebSocket API, you can optionally specify a template selection expression\. This expression is evaluated to determine the input or output template \(if any\) to use to transform either the request body into the integration request body \(via an input template\) or the integration response body to the route response body \(via an output template\)\.

`Integration.TemplateSelectionExpression` supports `${request.body.jsonPath}` and static values\.

`IntegratiionResponse.TemplateSelectionExpression` supports `${request.body.jsonPath}`, `${integration.response.statuscode}`, `${integration.response.header.headerName}`, `${integration.response.multivalueheader.headerName}`, and static values\.

## Route Response Selection Expressions<a name="apigateway-websocket-api-route-response-selection-expressions"></a>

A [route response](apigateway-websocket-api-route-response.md) is used for modeling a response from the backend to the client\. For WebSocket APIs, a route response is optional\. When defined, it signals to API Gateway that it should return a response to a client upon receiving a WebSocket message\.

Evaluation of the *route response selection expression* produces a route response key\. Eventually, this key will be used to choose from one of the [https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-routes-routeid-routeresponses.html](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-routes-routeid-routeresponses.html) associated with the API\. However, currently only the `$default` key is supported\.

## API Key Selection Expressions<a name="apigateway-websocket-api-apikey-selection-expressions"></a>

This expression is evaluated when the service determines the given request should proceed only if the client provides a valid [API key](api-gateway-basic-concept.md#apigateway-definition-api-key)\.

Currently the only two supported values are `$request.header.x-api-key` and `$context.authorizer.usageIdentifierKey`\.

## API Mapping Selection Expressions<a name="apigateway-websocket-api-mapping-selection-expressions"></a>

This expression is evaluated to determine which API stage is selected when a request is made using a custom domain\.

Currently, the only supported value is `$request.basepath`\.

## Integration Response Selection Expressions<a name="apigateway-websocket-api-integration-response-selection-expressions"></a>

When you [set up an integration response](apigateway-websocket-api-integration-responses.md) for a WebSocket API, you can optionally specify an integration response selection expression\. This expression determines what `[https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid-integrationresponses-integrationresponseid.html](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid-integrationresponses-integrationresponseid.html)` should be selected when an integration returns\. The value of this expression is currently restricted by API Gateway, as defined below\. Realize that this expression is only relevant for *non\-proxy integrations*; a proxy integration simply passes the response payload back to the caller without modeling or modification\.

Unlike the other selection expressions outlined above, this expression currently supports a *pattern\-matching* format\. The expression should be wrapped with forward slashes\.

Currently the value is fixed depending on the `[https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid.html#apis-apiid-integrations-integrationid-prop-integration-integrationtype](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid.html#apis-apiid-integrations-integrationid-prop-integration-integrationtype)`:
+ For Lambda\-based integrations, it is `$integration.response.body.errorMessage`\.
+ For `HTTP` and `MOCK` integrations, it is `$integration.response.statuscode`\.
+ For `HTTP_PROXY` and `AWS_PROXY`, the expression is not utilized, because you are requesting that the payload pass through to the caller\.

## WebSocket Selection Expression Summary<a name="apigateway-websocket-api-selection-expression-table"></a>

The following table summarizes the use cases for selection expressions in WebSocket APIs:


| Selection Expression | Evaluates to Key For | Notes | Example Use Case | 
| --- | --- | --- | --- | 
| Api\.RouteSelectionExpression | Route\.RouteKey | $default is supported as a catch\-all route\. | Route WebSocket messages based on the context of a client request\. | 
| Route\.ModelSelectionExpression | Key for Route\.RequestModels | Optional\. If provided for non\-proxy integration, model validation occurs\. `$default` is supported as a catch\-all\.  | Perform request validation dynamically within the same route\. | 
| Integration\.TemplateSelectionExpression | Key for Integration\.RequestTemplates |  Optional\. May be provided for non\-proxy integration to manipulate incoming payloads\. `${request.body.jsonPath}` and static values are supported\. `$default` is supported as a catch\-all\.  | Manipulate the caller's request based on dynamic properties of the request\. | 
| Integration\.IntegrationResponseSelectionExpression | IntegrationResponse\.IntegrationResponseKey |  Optional\. May be provided for non\-proxy integration\. Acts as a pattern match for error messages \(from Lambda\) or status codes \(from HTTP integrations\)\. `$default` is required for non\-proxy integrations to act as the catch\-all for successful responses\.  |  Manipulate the response from the backend\. Choose the action to occur based on the dynamic response of the backend \(e\.g\., handling certain errors distinctly\)\.  | 
| IntegrationResponse\.TemplateSelectionExpression | Key for IntegrationResponse\.ResponseTemplates | Optional\. May be provided for non\-proxy integration\. $default is supported\.  |  In some cases, a dynamic property of the response may dictate different transformations within the same route and associated integration\. `${request.body.jsonPath}`, `${integration.response.statuscode}`, `${integration.response.header.headerName}`, `${integration.response.multivalueheader.headerName}`, and static values are supported\. `$default` is supported as a catch\-all\.  | 
| Route\.RouteResponseSelectionExpression | RouteResponse\.RouteResponseKey |  Should be provided to initiate two\-way communication for a WebSocket route\. Currently, this value is restricted to `$default` only\.  |  | 
| RouteResponse\.ModelSelectionExpression | Key for RouteResponse\.RequestModels | Currently unsupported\. |  | 
