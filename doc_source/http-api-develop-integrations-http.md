# Working with HTTP proxy integrations for HTTP APIs<a name="http-api-develop-integrations-http"></a>

An HTTP proxy integration enables you to connect an API route to a publicly routable HTTP endpoint\. With this integration type, API Gateway passes the entire request and response between the frontend and the backend\. 

To create an HTTP proxy integration, provide the URL of a publicly routable HTTP endpoint\.

## HTTP proxy integration with path variables<a name="http-api-develop-integrations-http-proxy"></a>

You can use path variables in HTTP API routes\.

For example, the route `/pets/{petID}` catches requests to `/pets/6`\. You can reference path variables in the integration URI to send the variable's contents to an integration\. An example is `/pets/extentendedpath/{petID}`\.

You can use greedy path variables to catch all child resources of a route\. To create a greedy path variable, add `+` to the variable name—for example, `{proxy+}`\. 

To set up a route with an HTTP proxy integration that catches all requests, create an API route with a greedy path variable \(for example, `/parent/{proxy+}`\)\. Integrate the route with an HTTP endpoint \(for example, `https://petstore-demo-endpoint.execute-api.com/petstore/{proxy}`\) on the `ANY` method\. The greedy path variable must be at the end of the resource path\.