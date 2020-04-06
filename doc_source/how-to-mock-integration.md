# Set up mock integrations in API Gateway<a name="how-to-mock-integration"></a>

Amazon API Gateway supports mock integrations for API methods\. This feature enables API developers to generate API responses from API Gateway directly, without the need for an integration backend\. As an API developer, you can use this feature to unblock dependent teams that need to work with an API before the project development is complete\. You can also use this feature to provision a landing page for your API, which can provide an overview of and navigation to your API\. For an example of such a landing page, see the integration request and response of the GET method on the root resource of the example API discussed in [Tutorial: Create a REST API by importing an example](api-gateway-create-api-from-example.md)\.

As an API developer, you decide how API Gateway responds to a mock integration request\. For this, you configure the method's integration request and integration response to associate a response with a given status code\. For a method with the mock integration to return a `200` response, configure the integration request body mapping template to return the following\.

```
{"statusCode": 200}
```

Configure a `200` integration response to have the following body mapping template, for example:

```
{
    "statusCode": 200,
    "message": "Go ahead without me."
}
```

 Similarly, for the method to return, for example, a `500` error response, set up the integration request body mapping template to return the following\.

```
{"statusCode": 500}
```

Set up a `500` integration response with, for example, the following mapping template: 

```
{
    "statusCode": 500,
    "message": "The invoked method is not supported on the API resource."
}
```

Alternatively, you can have a method of the mock integration return the default integration response without defining the integration request mapping template\. The default integration response is the one with an undefined **HTTP status regex**\. Make sure appropriate passthrough behaviors are set\.

**Note**  
Mock integrations aren't intended to support large response templates\. If you need them for your use case, you should consider using a Lambda integration instead\.

Using an integration request mapping template, you can inject application logic to decide which mock integration response to return based on certain conditions\. For example, you could use a `scope` query parameter on the incoming request to determine whether to return a successful response or an error response:

```
{
  #if( $input.params('scope') == "internal" )
    "statusCode": 200
  #else
    "statusCode": 500
  #end
}
```

This way, the method of the mock integration lets internal calls to go through while rejecting other types of calls with an error response\. 

In this section, we describe how to use the API Gateway console to enable the mock integration for an API method\.

**Topics**
+ [Enable mock integration using the API Gateway console](how-to-mock-integration-console.md)