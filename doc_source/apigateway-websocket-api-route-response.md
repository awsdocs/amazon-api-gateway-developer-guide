# Set up route responses for a WebSocket API in API Gateway<a name="apigateway-websocket-api-route-response"></a>

WebSocket routes can be configured for two\-way or one\-way communication\. API Gateway will not pass the backend response through to the route response, unless you set up a route response\. 

**Note**  
You can only define the `$default` route response for WebSocket APIs\. You can use an integration response to manipulate the response from a backend service\. For more information, see [Overview of integration responses](apigateway-websocket-api-integration-responses.md#apigateway-websocket-api-integration-response-overview)\. 

You can configure route responses and response selection expressions by using the API Gateway console or the AWS CLI or an AWS SDK\. 

For more information about route response selection expressions, see [Route response selection expressions](apigateway-websocket-api-selection-expressions.md#apigateway-websocket-api-route-response-selection-expressions)\.

**Topics**
+ [Set up a route response using the API Gateway console](#apigateway-websocket-api-route-response-using-console)
+ [Set up a route response using the AWS CLI](#apigateway-websocket-api-route-response-using-awscli)

## Set up a route response using the API Gateway console<a name="apigateway-websocket-api-route-response-using-console"></a>

To set up a route response for the `$default` route with a proxy Lambda function integration using the API Gateway console:

1. Sign in to the API Gateway console, choose a WebSocket API with a proxy Lambda function integration on the `$default` route\.

1. Under **Routes**, choose the `$default` route\.

1. Choose **Route Request** in the route overview pane\.

1. Under **Route Request Modeling**, enter **default** as the **Model Selection Expression** and choose the checkmark icon\.

1. Choose **Add model**, enter **default** as the **New Model Key** and choose the checkmark icon\.

1. Choose **Route Overview**\.

1.  Choose **Add Integration Response**\.

1. Choose **Route Response**\.

1. Under **Response Modeling**, enter **default** as the response selection expression\.

1. Under **Route Responses**, enter **$default**\.

1.  Under the **Actions** tab, choose **Deploy API**\.

1.  Use the following [ wscat](https://www.npmjs.com/package/wscat) command to connect to your API\. For more information about `wscat`, see [Use `wscat` to connect to a WebSocket API and send messages to it](apigateway-how-to-call-websocket-api-wscat.md)\. 

   ```
   wscat -c wss://api-id.execute-api.us-east-2.amazonaws.com/test
   ```

    Press the enter button to call the default route\. The body of your Lambda function should return\.

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