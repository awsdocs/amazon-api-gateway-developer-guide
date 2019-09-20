# Specify Route Request Settings<a name="apigateway-websocket-api-route-route-request"></a>

## Specify Route Request Settings for `$connect`<a name="apigateway-websocket-api-route-request-connect"></a>

**Topics**

When you set up the `$connect` route for your API, the following optional settings are available to enable authorization for your API\. For more information, see [The `$connect` Route](apigateway-websocket-api-route-keys-connect-disconnect.md#apigateway-websocket-api-routes-about-connect)\.
+ **Authorization**: If no authorization is needed, you can specify `NONE`\. Otherwise, you can specify: 
  + `AWS_IAM` to use standard AWS IAM policies to control access to your API\. 
  + `CUSTOM` to implement authorization for an API by specifying a Lambda authorizer function that you have previously created\. The authorizer can reside in your own AWS account or a different AWS account\. For more information about Lambda authorizers, see [Use API Gateway Lambda Authorizers](apigateway-use-lambda-authorizer.md)\.
**Note**  
In the API Gateway console, the `CUSTOM` setting is visible only after you have set up an authorizer function as described in [Configure a Lambda Authorizer Using the API Gateway Console](configure-api-gateway-lambda-authorization-with-console.md)\.
**Important**  
The **Authorization** setting is applied to the entire API, not just the `$connect` route\. The `$connect` route protects the other routes, because it is called on every connection\.
+ **API Key Required**: You can optionally require an API key for an API's `$connect` route\. You can use API keys together with usage plans to control and track access to your APIs\. For more information, see [Create and Use Usage Plans with API Keys](api-gateway-api-usage-plans.md)\.

## Set up the `$connect` Route Request Using the API Gateway Console<a name="apigateway-websocket-api-connect-route-request-using-console"></a>

To set up the `$connect` route request for a WebSocket API using the API Gateway console:

1. Sign in to the API Gateway console, choose the API, and choose **Routes**\.

1. Under **Routes**, choose `$connect`\.

1. Choose **Route Request** in the route overview pane\.

1. Under **Access Settings**, configure the route settings as follows:

   1. To edit the **Authorization** setting, choose the pencil icon\. Choose the desired setting from the dropdown menu and choose the checkmark icon to save the new setting\.

   1. To edit the **API Key Required** setting, choose the pencil icon\. Choose `true` or `false` from the dropdown menu and choose the checkmark icon to save the new setting\.