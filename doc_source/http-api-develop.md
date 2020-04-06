# Developing an HTTP API in API Gateway<a name="http-api-develop"></a>

This section provides details about API Gateway capabilities that you need while you're developing your API Gateway APIs\.

As you're developing your API Gateway API, you decide on a number of characteristics of your API\. These characteristics depend on the use case of your API\. For example, you might want to only allow certain clients to call your API, or you might want it to be available to everyone\. You might want an API call to execute a Lambda function, make a database query, or call an application\.

**Topics**
+ [Creating an HTTP API](#http-api-examples)
+ [Working with routes for HTTP APIs](http-api-develop-routes.md)
+ [Controlling access to HTTP APIs with JWT authorizers](http-api-jwt-authorizer.md)
+ [Configuring integrations for HTTP APIs](http-api-develop-integrations.md)
+ [Configuring CORS for an HTTP API](http-api-cors.md)
+ [Working with OpenAPI definitions for HTTP APIs](http-api-open-api.md)

## Creating an HTTP API<a name="http-api-examples"></a>

To create a functional API, you must have at least one route, integration, stage, and deployment\. 

The following examples show how to create an API with an AWS Lambda or HTTP integration, a route, and a default stage that is configured to automatically deploy changes\.

**Topics**
+ [Create an HTTP API by using the AWS Management Console](#apigateway-http-api-create.console)
+ [Create an HTTP API by using the AWS CLI](#http-api-examples.cli.quick-create)

### Create an HTTP API by using the AWS Management Console<a name="apigateway-http-api-create.console"></a>

1. Open the [API Gateway console](https://console.aws.amazon.com/apigateway)\.

1. Choose **Create API**\.

1. Under **HTTP API**, choose **Build**\.

1. Choose **Add integration**, and then choose an AWS Lambda function or enter an HTTP endpoint\.

1. For **Name**, enter a name for your API\.

1. Choose **Review and create**\.

1. Choose **Create**\.

Now your API is ready to invoke\. You can test your API by entering its invoke URL in a browser, or by using Curl\.

```
curl https://api-id.execute-api.us-east-2.amazonaws.com
```

### Create an HTTP API by using the AWS CLI<a name="http-api-examples.cli.quick-create"></a>

You can use [quick create](api-gateway-basic-concept.md#apigateway-definition-quick-create) to create an API with a Lambda or HTTP integration, a default catch\-all route, and a default stage that is configured to automatically deploy changes\. The following command uses quick create to create an API that integrates with a Lambda function on the backend\.

**Note**  
To invoke a Lambda integration, API Gateway must have the required permissions\. You can use a resource\-based policy or an IAM role to grant API Gateway permissions to invoke a Lambda function\. To learn more, see [AWS Lambda Permissions](https://docs.aws.amazon.com/lambda/latest/dg/lambda-permissions) in the * AWS Lambda Developer Guide*\.

**Example**  

```
aws apigatewayv2 create-api --name my-api --protocol-type HTTP --target arn:aws:lambda:us-east-2:123456789012:function:function-name
```

Now your API is ready to invoke\. You can test your API by entering its invoke URL in a browser, or by using Curl\.

```
curl https://api-id.execute-api.us-east-2.amazonaws.com
```