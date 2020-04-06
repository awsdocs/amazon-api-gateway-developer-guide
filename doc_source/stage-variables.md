# Setting up stage variables for a REST API deployment<a name="stage-variables"></a>

Stage variables are name\-value pairs that you can define as configuration attributes associated with a deployment stage of a REST API\. They act like environment variables and can be used in your API setup and mapping templates\.

For example, you can define a stage variable in a stage configuration, and then set its value as the URL string of an HTTP integration for a method in your REST API\. Later, you can reference the URL string by using the associated stage variable name from the API setup\. By doing this, you can use the same API setup with a different endpoint at each stage by resetting the stage variable value to the corresponding URLs\. 

You can also access stage variables in the mapping templates, or pass configuration parameters to your AWS Lambda or HTTP backend\.

For more information about mapping templates, see [API Gateway mapping template and access logging variable reference](api-gateway-mapping-template-reference.md)\.

## Use cases<a name="stage-variables-use-cases"></a>

With deployment stages in API Gateway, you can manage multiple release stages for each API, such as alpha, beta, and production\. Using stage variables you can configure an API deployment stage to interact with different backend endpoints\. 

For example, your API can pass a GET request as an HTTP proxy to the backend web host \(for example, `http://example.com`\)\. In this case, the backend web host is configured in a stage variable so that when developers call your production endpoint, API Gateway calls example\.com\. When you call your beta endpoint, API Gateway uses the value configured in the stage variable for the beta stage, and calls a different web host \(for example, `beta.example.com`\)\. Similarly, stage variables can be used to specify a different AWS Lambda function name for each stage in your API\.

You can also use stage variables to pass configuration parameters to a Lambda function through your mapping templates\. For example, you might want to reuse the same Lambda function for multiple stages in your API, but the function should read data from a different Amazon DynamoDB table depending on which stage is being called\. In the mapping templates that generate the request for the Lambda function, you can use stage variables to pass the table name to Lambda\.

Stage variables are not applied to the security definitions section of the API specification\. For example, you cannot use different Amazon Cognito user pools for different stages\.

## Examples<a name="stage-variables-examples"></a>

To use a stage variable to customize the HTTP integration endpoint, you must first configure a stage variable of a specified name \(for example, **url**\), and then assign it a value, \(for example, **example\.com**\)\. Next, from your method configuration, set up an HTTP proxy integration\. Instead of entering the endpoint's URL, you can tell API Gateway to use the stage variable value, **http://$\{stageVariables\.url\}**\. This value tells API Gateway to substitute your stage variable `${}` at runtime, depending on which stage your API is running\. 

You can reference stage variables in a similar way to specify a Lambda function name, an AWS Service Proxy path, or an AWS role ARN in the credentials field\.

When specifying a Lambda function name as a stage variable value, you must configure the permissions on the Lambda function manually\. You can use the AWS Command Line Interface \(AWS CLI\) to do this\.

```
aws lambda add-permission --function-name arn:aws:lambda:XXXXXX:your-lambda-function-name --source-arn arn:aws:execute-api:us-east-1:YOUR_ACCOUNT_ID:api_id/*/HTTP_METHOD/resource --principal apigateway.amazonaws.com --statement-id apigateway-access --action lambda:InvokeFunction
```

The following example assigns API Gateway permission to invoke a Lambda function named `helloWorld` hosted in the US West \(Oregon\) Region of an AWS account on behalf of the API method\.

```
arn arn:aws:execute-api:us-west-2:123123123123:bmmuvptwze/*/GET/hello
```

Here is the same command using the AWS CLI\.

```
aws lambda add-permission --function-name arn:aws:lambda:us-east-1:123123123123:function:helloWorld --source-arn  arn:aws:execute-api:us-west-2:123123123123:bmmuvptwze/*/GET/hello --principal apigateway.amazonaws.com --statement-id apigateway-access --action lambda:InvokeFunction
```