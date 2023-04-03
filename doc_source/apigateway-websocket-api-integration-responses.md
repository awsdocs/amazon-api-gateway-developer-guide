# Setting up a WebSocket API integration responses in API Gateway<a name="apigateway-websocket-api-integration-responses"></a>

**Topics**
+ [Overview of integration responses](#apigateway-websocket-api-integration-response-overview)
+ [Integration responses for two\-way communication](#apigateway-websocket-api-integration-response-for-two-way-communication)
+ [Set up an integration response using the API Gateway console](#apigateway-websocket-api-integration-response-using-console)
+ [Set up an integration response using the AWS CLI](#apigateway-websocket-api-integration-response-using-awscli)

## Overview of integration responses<a name="apigateway-websocket-api-integration-response-overview"></a>

API Gateway's integration response is a way of modeling and manipulating the response from a backend service\. There are some differences in setup of a REST API versus a WebSocket API integration response, but conceptually the behavior is the same\.

WebSocket routes can be configured for two\-way or one\-way communication\.
+ When a route is configured for two\-way communication, an integration response allows you to configure transformations on the returned message payload, similar to integration responses for REST APIs\.
+ If a route is configured for one\-way communication, then regardless of any integration response configuration, no response will be returned over the WebSocket channel after the message is processed\.

 API Gateway will not pass the backend response through to the route response, unless you set up a route response\. To learn about setting up a route response, see [Set up route responses for a WebSocket API in API Gateway](apigateway-websocket-api-route-response.md)\.

## Integration responses for two\-way communication<a name="apigateway-websocket-api-integration-response-for-two-way-communication"></a>

Integrations can be divided into *proxy* integrations and *non\-proxy* integrations\.

**Important**  
For *proxy integrations*, API Gateway automatically passes the backend output to the caller as the complete payload\. There is no integration response\.

For *non\-proxy integrations*, you must set up at least one integration response:
+ Ideally, one of your integration responses should act as a catch\-all when no explicit choice can be made\. This default case is represented by setting an integration response key of `$default`\.
+ In all other cases, the integration response key functions as a regular expression\. It should follow a format of `"/expression/"`\.

For non\-proxy HTTP integrations:
+ API Gateway will attempt to match the HTTP status code of the backend response\. The integration response key will function as a regular expression in this case\. If a match cannot be found, then `$default` is chosen as the integration response\.
+ The template selection expression, as described above, functions identically\. For example:
  + `/2\d\d/`: Receive and transform successful responses
  + `/4\d\d/`: Receive and transform bad request errors
  + `$default`: Receive and transform all unexpected responses

For more information about template selection expressions, see [Template selection expressions](websocket-api-data-transformations.md#apigateway-websocket-api-template-selection-expressions)\.

## Set up an integration response using the API Gateway console<a name="apigateway-websocket-api-integration-response-using-console"></a>

To set up a route integration response for a WebSocket API using the API Gateway console:

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1.  Choose your WebSocket API and choose your route\.

1. Choose **Integration Request** in the route overview pane\.

1. Under **Request Templates**, enter **default** as the template selection expression\. This will receive and transform all responses\. You can use other integration response key functions for your API, for example, you can enter `/4\d\d/` to receive and transform bad request errors\. 

1.  Choose **Route Overview**\.

1. Choose **Add Integration Response**\.

1. Under **Integration Responses**, choose **Add Response**\.

1. Under **Response Key**, enter **$default** and choose the checkmark icon\.

1. You can also define a mapping template to configure transformations of your returned message payload\. Choose the pencil icon next to **Template Selection Expression**\. 

1. Enter a key name\. If you are choosing the default template selection expression, enter **\\$default**\. Choose the checkmark icon\. 

1. Under **Template Key**, choose **Add response template**\.

1. Enter a Template Key name and choose the checkmark icon\.

1. Enter your mapping template in the code editor\.

1.  Choose **Save**\.

1. Under the **Actions** tab, choose **Deploy API**\.

1.  Use the following [ wscat](https://www.npmjs.com/package/wscat) command to connect to your API\. For more information about `wscat`, see [Use `wscat` to connect to a WebSocket API and send messages to it](apigateway-how-to-call-websocket-api-wscat.md)\. 

   ```
   wscat -c wss://api-id.execute-api.us-east-2.amazonaws.com/test
   ```

    When you call your route, the returned message payload should return\. 

## Set up an integration response using the AWS CLI<a name="apigateway-websocket-api-integration-response-using-awscli"></a>

To set up an integration response for a WebSocket API using the AWS CLI call the [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateIntegrationResponse](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateIntegrationResponse) command\. The following CLI command shows an example of creating a `$default` integration response:

```
aws apigatewayv2 create-integration-response \
    --api-id vaz7da96z6 \
    --integration-id a1b2c3 \
    --integration-response-key '$default'
```