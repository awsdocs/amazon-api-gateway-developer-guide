# HTTP API Concepts<a name="http-api-concepts"></a>


|  | 
| --- |
| HTTP APIs are in beta for Amazon API Gateway and are subject to change\. | 

The following are key concepts for HTTP APIs\.

**Topics**
+ [Routes](#http-api-concepts.routes)
+ [Integrations](#http-api-concepts.integrations)
+ [Stages](#http-api-concepts.stages)
+ [Deployments](#http-api-concepts.deployments)
+ [CORS Configuration](#http-api-concepts.cors)
+ [JWT Authorizers](#http-api-concepts.authorizers)
+ [Quick Create](#http-api-concepts.quick-create)
+ [JWT Authorizers](http-api-jwt-authorizer.md)
+ [Configuring CORS for an HTTP API](http-api-cors.md)

## Routes<a name="http-api-concepts.routes"></a>

Routes direct API requests to backend resources\. Routes consist of two parts: an HTTP method and a resource path—for example, `GET /pets`\. You can define specific HTTP methods for your integration, or use the ANY method to match all methods that you haven't defined for a resource\. You can create a `$default` route that acts as a catch\-all for requests that don’t match any other routes\. 

## Integrations<a name="http-api-concepts.integrations"></a>

Integrations connect a route to backend resources\. HTTP APIs support Lambda proxy and HTTP proxy integrations\. For example, you can configure a `POST` request to the `/signup` route of your API to integrate with a Lambda function that handles signing up customers\.

To create a Lambda integration, provide the Amazon Resource Name \(ARN\) of a Lambda function\.

To create an HTTP integration, provide the URL of a publicly routable HTTP endpoint\.

## Stages<a name="http-api-concepts.stages"></a>

A stage represents a version of an API\. For example, you can create `dev`, `test`, and `prod` stages of your API\. 

You can create a `$default` stage that is served from the base of your API's URL—for example, `https://{api_id}.execute-api.{region}.amazonaws.com/`\. You use this URL to invoke an API stage\.

If you enable automatic deployments, then changes to an API are automatically released for you\.

## Deployments<a name="http-api-concepts.deployments"></a>

A deployment is a snapshot of your API configuration\. After you deploy an API to a stage, it’s available for clients to invoke\. You must deploy an API for changes to take effect\. If you enable automatic deployments, then changes to an API are automatically released for you\.

## CORS Configuration<a name="http-api-concepts.cors"></a>

[Cross\-origin resource sharing \(CORS\)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) is a browser security feature that restricts HTTP requests from a different domain, or origin\. HTTP APIs support CORS configuration to simplify CORS support for web applications\. To learn more, see [Configuring CORS for an HTTP API](http-api-cors.md)\.

## JWT Authorizers<a name="http-api-concepts.authorizers"></a>

HTTP APIs support JSON Web Token \(JWT\) authorizers\. You can use standard [OpenID Connect \(OIDC\)](https://openid.net/specs/openid-connect-core-1_0.html) and [OAuth 2\.0](https://oauth.net/2/) frameworks to authorize requests to your API\. To learn more, see [JWT Authorizers](http-api-jwt-authorizer.md)\.

## Quick Create<a name="http-api-concepts.quick-create"></a>

You can use quick create to simplify creating an API\. Quick create creates an API with a Lambda or HTTP integration, a default catch\-all route, and a default stage that is configured to automatically deploy changes\. For more information, see [Create an HTTP API by Using the AWS CLI](http-api-examples.md#http-api-examples.cli.quick-create)\.