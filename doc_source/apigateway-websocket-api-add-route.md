# Set up Routes for a WebSocket API in API Gateway<a name="apigateway-websocket-api-add-route"></a>

When you first create a new WebSocket API, there are three predefined routes: `$connect`, `$disconnect`, and `$default`\. You can create them by using the console, API, or AWS CLI\. If desired, you can create custom routes\. For more information, see [About WebSocket APIs in API Gateway](apigateway-websocket-api-overview.md)\.

**Note**  
In the CLI, you can create routes before or after you create integrations, and you can reuse the same integration for multiple routes\.

**Topics**
+ [Create a Route Using the API Gateway Console](#apigateway-websocket-api-route-using-console)
+ [Create a Route Using the AWS CLI](#apigateway-websocket-api-route-using-awscli)

## Create a Route Using the API Gateway Console<a name="apigateway-websocket-api-route-using-console"></a>

**To create a route using the API Gateway console**

1. Sign in to the API Gateway console, choose the API, and choose **Routes**\.

1. To create one of the predefined routes \(`$connect`, `$disconnect`, and `$default`\), choose its name\.

1. If desired, you can create custom routes\. To do so, enter the route key name in the **New Route Key** text box and choose the checkmark icon\.
**Note**  
When you create a custom route, do not use the `$` prefix in the route key name\. This prefix is reserved for predefined routes\.

## Create a Route Using the AWS CLI<a name="apigateway-websocket-api-route-using-awscli"></a>

To create a route using the AWS CLI, call [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateRoute](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateRoute) as shown in the following example:

```
aws apigatewayv2 --region us-east-1 create-route --api-id aabbccddee --route-key $default
```

Example output:

```
{
    "ApiKeyRequired": false,
    "AuthorizationType": "NONE",
    "RouteKey": "$default",
    "RouteId": "1122334"
}
```