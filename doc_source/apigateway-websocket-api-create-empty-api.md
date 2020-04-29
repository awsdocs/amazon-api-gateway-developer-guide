# Create a WebSocket API in API Gateway<a name="apigateway-websocket-api-create-empty-api"></a>

You can create a WebSocket API in the API Gateway console, by using the AWS CLI [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateApi](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateApi) command, or by using the `CreateApi` command in an AWS SDK\. The following procedures show how to create a new WebSocket API\.

**Note**  
WebSocket APIs only support TLS 1\.2\. Earlier TLS versions are not supported\.

## Create a WebSocket API using AWS CLI commands<a name="apigateway-websocket-api-create-using-awscli"></a>

Creating a WebSocket API using the AWS CLI requires calling the [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateApi](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateApi) command as shown in the following example, which creates an API with the `$request.body.action` route selection expression:

```
aws apigatewayv2 --region us-east-1 create-api --name "myWebSocketApi3" --protocol-type WEBSOCKET --route-selection-expression '$request.body.action'
```

Example output:

```
{
    "ApiKeySelectionExpression": "$request.header.x-api-key",
    "Name": "myWebSocketApi3",
    "CreatedDate": "2018-11-15T06:23:51Z",
    "ProtocolType": "WEBSOCKET",
    "RouteSelectionExpression": "'$request.body.action'",
    "ApiId": "aabbccddee"
}
```

## Create a WebSocket API using the API Gateway console<a name="apigateway-websocket-api-create-using-console"></a>

You can create a WebSocket API in the console by choosing the WebSocket protocol and giving the API a name\.

**Important**  
Once you have created the API, you cannot change the protocol you have chosen for it\. There is no way to convert a WebSocket API into a REST API or vice versa\.

**To create a WebSocket API using the API Gateway console**

1. Sign in to the API Gateway console and choose **Create API**\.

1. Under **WebSocket API**, choose **Build**\.

1. Under **Settings**, in the **API name** field, enter the name of your API, for example, **PetStore**\.

1. Enter a **Route Selection Expression** for your API, for example, `$request.body.action`\.

   For more information about route selection expressions, see [Route selection expressions](websocket-api-develop-routes.md#apigateway-websocket-api-route-selection-expressions)\.

1. If desired, enter a **Description** for your API\.

1. Choose **Create API**\.