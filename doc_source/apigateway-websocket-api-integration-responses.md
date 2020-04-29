# Setting up a WebSocket API integration responses in API Gateway<a name="apigateway-websocket-api-integration-responses"></a>

**Topics**
+ [Overview of integration responses](#apigateway-websocket-api-integration-response-overview)
+ [Set up an integration response using the API Gateway console](#apigateway-websocket-api-integration-response-using-console)
+ [Set up an integration response using the AWS CLI](#apigateway-websocket-api-integration-response-using-awscli)

## Overview of integration responses<a name="apigateway-websocket-api-integration-response-overview"></a>

API Gateway's integration response is a way of modeling and manipulating the response from a backend service\. There are some differences in setup of a REST API versus a WebSocket API integration response, but conceptually the behavior is the same\.

WebSocket routes can be configured for two\-way or one\-way communication\. If a route has a route response, it is configured for two\-way communication\. Otherwise, it is configured for one\-way communication\.
+ When a route is configured for two\-way communication, an integration response allows you to configure transformations on the returned message payload, similar to integration responses for REST APIs\.
+ If a route is configured for one\-way communication, then regardless of any integration response configuration, no response will be returned over the WebSocket channel after the message is processed\.

The remainder of this document assumes you have chosen to configure a route with two\-way communication\.

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

For current limitations of template selection expressions, see [Template selection expressions](websocket-api-data-transformations.md#apigateway-websocket-api-template-selection-expressions)\.

## Set up an integration response using the API Gateway console<a name="apigateway-websocket-api-integration-response-using-console"></a>

To set up a route integration response for a WebSocket API using the API Gateway console:

1. Sign in to the API Gateway console, choose the API, and choose **Routes**\.

1. Choose the route\.

1. Choose **Integration Response**\.

1. Under **Integration Responses**, enter a value in the **Response Selection Expression** text box\.

1. Under **Response Key**, choose **Add Response**\.

   1. To define an integration response key, enter a key name in the **New Response Key** text box and choose the checkmark icon\.

   1. Choose the pencil icon next to **Template Selection Expression** and enter an expression for API Gateway to look for in your outgoing message\. This expression should evaluate to an integration response key value that maps to one of your response templates\.

      For information about template selection expressions, see [Template selection expressions](websocket-api-data-transformations.md#apigateway-websocket-api-template-selection-expressions)\.

## Set up an integration response using the AWS CLI<a name="apigateway-websocket-api-integration-response-using-awscli"></a>

To set up an integration response for a WebSocket API using the AWS CLI call the [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateIntegrationResponse](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateIntegrationResponse) command\. The following CLI command shows an example of creating a `$default` integration response:

```
aws apigatewayv2 create-integration-response \
    --api-id vaz7da96z6 \
    --integration-id a1b2c3 \
    --integration-response-key '$default'
```