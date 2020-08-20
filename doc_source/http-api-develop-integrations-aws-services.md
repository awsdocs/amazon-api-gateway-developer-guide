# Working with AWS service integrations for HTTP APIs<a name="http-api-develop-integrations-aws-services"></a>

You can integrate your HTTP API with AWS services by using *first\-class integrations*\. A first\-class integration connects an HTTP API route to an AWS service API\. When a client invokes a route that's backed by a first\-class integration, API Gateway invokes an AWS service API for you\. For example, you can use first\-class integrations to send a message to an Amazon Simple Queue Service queue, or to start an AWS Step Functions state machine\. For supported service actions, see [Integration subtype reference](http-api-develop-integrations-aws-services-reference.md)\.

## Mapping request parameters<a name="http-api-develop-integrations-aws-services-parameter-mapping"></a>

First\-class integrations have required and optional parameters\. You must configure all required parameters to create an integration\. You can use static values or map parameters that are dynamically evaluated at runtime\. For a full list of supported integrations and parameters, see [Integration subtype reference](http-api-develop-integrations-aws-services-reference.md)\.


**Parameter mapping**  

| Type | Example | Notes | 
| --- | --- | --- | 
| Header value | $request\.header\.name | Header names are case\-insensitive\. API Gateway combines multiple header values with commas, for example "header1": "value1,value2"\. | 
| Query string value | $request\.querystring\.name | Query string names are case\-sensitive\. API Gateway combines multiple values with commas, for example "querystring1": "Value1,Value2"\. | 
| Path parameter | $request\.path\.name | The value of a path parameter in the request\. For example if the route is /pets/\{petId\}, you can map the petId parameter from the request with $request\.path\.petId\. | 
| Request body passthrough | $request\.body | API Gateway passes the entire request body through\. | 
| Request body | $request\.body\.name | A [JSON path expression](https://goessner.net/articles/JsonPath/index.html#e2)\. Recursive descent \($request\.body\.\.name\) and filter expressions \(?\(expression\)\) aren't supported\. | 
| Context variable | $context\.variableName | The value of a supported [context variable](http-api-logging-variables.md)\. | 
| Stage variable | $stageVariables\.variableName | The value of a [stage variable](http-api-stages.md#http-api-stages.stage-variables)\. | 
| Static value | string | A constant value\. | 

## Create a first\-class integration<a name="http-api-develop-integrations-aws-services-example"></a>

Before you create a first\-class integration, you must create an IAM role that grants API Gateway permissions to invoke the AWS service action that you're integrating with\. To learn more, see [Creating a role for an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html)\.

To create a first\-class integration, choose a supported AWS service action, such as `SQS-SendMessage`, configure the request parameters, and provide a role that grants API Gateway permissions to invoke the integrated AWS service API\. Depending on the integration subtype, different request parameters are required\. To learn more, see [Integration subtype reference](http-api-develop-integrations-aws-services-reference.md)\.

The following AWS CLI command creates an integration that sends an Amazon SQS message\.

```
aws apigatewayv2 create-integration \
    --api-id abcdef123 \
    --integration-subtype SQS-SendMessage \
    --integration-type AWS_PROXY \
    --payload-format-version 1.0 \
    --credentials-arn arn:aws:iam::123456789012:role/apigateway-sqs \
    --request-parameters '{"QueueUrl": "$request.header.queueUrl", "MessageBody": "$request.body.message"}'
```