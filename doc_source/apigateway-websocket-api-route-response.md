# Set up route responses for a WebSocket API in API Gateway<a name="apigateway-websocket-api-route-response"></a>

WebSocket routes can be configured for two\-way or one\-way communication\. If a route has a route response, it is configured for two\-way communication\. Otherwise, it is configured for one\-way communication\. *Route responses* are used to enable two\-way communication, in which your API can send a response back to the client in the context of the client's message\.

You can configure route responses and response selection expressions by using the API Gateway console or the AWS CLI or an AWS SDK\. For more information about route responses, see [Invoking your backend integration: `$default` Route and custom routes](apigateway-websocket-api-routes-integrations.md)\.

For more information about route response selection expressions, see [Route response selection expressions](apigateway-websocket-api-selection-expressions.md#apigateway-websocket-api-route-response-selection-expressions)\.

**Topics**
+ [Set up a route response using the API Gateway console](#apigateway-websocket-api-route-response-using-console)
+ [Set up a route response using the AWS CLI](#apigateway-websocket-api-route-response-using-awscli)

## Set up a route response using the API Gateway console<a name="apigateway-websocket-api-route-response-using-console"></a>

To set up a route response for a WebSocket API using the API Gateway console:

1. Sign in to the API Gateway console, choose the API\.

1. Under **Routes**, choose the route\.

1. Choose **Route Response** in the route overview pane\.

1. Under **Response Modeling**, in the **Response Selection Expression** box, enter the desired response selection expression and choose the checkmark icon\.

1. Under **Route Responses**, under **Response Key**, choose **Add Response**\.
**Note**  
Currently only `$default` is supported in route responses for WebSocket APIs\.

1. Enter the response key name and choose the checkmark icon\.

## Set up a route response using the AWS CLI<a name="apigateway-websocket-api-route-response-using-awscli"></a>

To set up a route response for a WebSocket API using the AWS CLI, call the [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateRouteResponse](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateRouteResponse) command as shown in the following example\. You can identify the API ID and route ID by calling [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/GetApis](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/GetApis) and [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/GetRoutes](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/GetRoutes)\.

```
aws apigatewayv2 create-route-response \
    --api-id aabbccddee \
    --route-id 1122334  \
    --route-response-key '$default'
```

Example output:

```
{
    "RouteResponseId": "abcdef",
    "RouteResponseKey": "$default"
}
```