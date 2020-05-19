# Working with routes for HTTP APIs<a name="http-api-develop-routes"></a>

Routes direct incoming API requests to backend resources\. Routes consist of two parts: an HTTP method and a resource path—for example, `GET /pets`\. You can define specific HTTP methods for your route\. Or, you can use the `ANY` method to match all methods that you haven't defined for a resource\. You can create a `$default` route that acts as a catch\-all for requests that don’t match any other routes\.

## Working with path variables<a name="http-api-routes-path-variables"></a>

You can use path variables in HTTP API routes\.

For example, the `GET /pets/{petID}` route catches a `GET` request that a client submits to `https://api-id.execute-api.us-east-2.amazonaws.com/pets/6`\. 

A *greedy path variable* catches all child resources of a route\. To create a greedy path variable, add `+` to the variable name—for example, `{proxy+}`\. The greedy path variable must be at the end of the resource path\.

## Working with query string parameters<a name="http-api-routes-path-variables"></a>

By default, API Gateway sends query string parameters to your backend integration if they are included in a request to an HTTP API\.

For example, when a client sends a request to `https://api-id.execute-api.us-east-2.amazonaws.com/pets?id=4&type=dog`, the query string parameters `?id=4&type=dog` are sent to your integration\.

## Working with the `$default` route<a name="http-api-develop-routes.default"></a>

The `$default` route catches requests that don't explicitly match other routes in your API\.

When the `$default` route receives a request, API Gateway sends the full request path to the integration\. For example, you can create an API with only a `$default` route and integrate it on the `ANY` method with the `https://petstore-demo-endpoint.execute-api.com` HTTP endpoint\. When you send a request to `https://api-id.execute-api.us-east-2.amazonaws.com/store/checkout`, API Gateway sends a request to `https://petstore-demo-endpoint.execute-api.com/store/checkout`\.

To learn more about HTTP integrations, see [Working with HTTP proxy integrations for HTTP APIs](http-api-develop-integrations-http.md)\.

## Routing API requests<a name="http-api-develop-routes.evaluation"></a>

When a client sends an API request, API Gateway first determines which [stage](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-stages.html) to route the request to\. If the request explicitly matches a stage, API Gateway sends the request to that stage\. If no stage fully matches the request, API Gateway sends the request to the `$default` stage\. If there's no `$default` stage, then the API returns `{"message":"Not Found"}`\.

After selecting a stage, API Gateway selects a route\. API Gateway selects the route with the most\-specific match, using the following priorities:

1. Full match for a route and method\.

1. Match for a route and method with a greedy path variable \(`{proxy+}`\)\.

1. The `$default` route\.

If no routes match a request, API Gateway returns `{"message":"Not Found"}` to the client\.

For example, consider an API with a `$default` stage and the following example routes:

1. `GET /pets/dog/{id}`

1. `GET /pets/{proxy+}`

1. `ANY /{proxy+}`

1. `$default`

   The following table summarizes how API Gateway routes requests to the example routes\.


| Request | Selected route | Explanation | 
| --- | --- | --- | 
|  `GET https://api-id.execute-api.region.amazonaws.com/pets/dog/1`  |  `GET /pets/dog/{id}`  |  The request fully matches this route\.  | 
|  `GET https://api-id.execute-api.region.amazonaws.com/pets/cat/1`  |  `GET /pets/{proxy+}`  |  The request doesn't fully match a route\. The route with a `GET` method and a greedy path variable catches this request\.  | 
| `POST https://api-id.execute-api.region.amazonaws.com/test/5` | `ANY /{proxy+}` |  The `ANY` method matches all methods that you haven't defined for a route\. Routes with greedy path variables have higher priority than the `$default` route\.  | 