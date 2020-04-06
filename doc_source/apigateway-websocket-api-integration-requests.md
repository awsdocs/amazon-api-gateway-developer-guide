# Setting up a WebSocket API integration request in API Gateway<a name="apigateway-websocket-api-integration-requests"></a>

Setting up an integration request involves the following:
+ Choosing a route key to integrate to the backend\.
+ Specifying the backend endpoint to invoke, such as an AWS service or HTTP endpoint\.
+ Configuring how to transform the route request data, if necessary, into integration request data by specifying one or more request templates\.

## Set up a WebSocket API integration request using the API Gateway console<a name="apigateway-websocket-api-integration-request-using-console"></a>

**To add an integration request to a route in a WebSocket API using the API Gateway console**

1. Sign in to the API Gateway console, choose the API, and choose **Routes**\.

1. Under **Routes**, choose the route\.

1. In the **Route Overview** pane, choose **Integration Request**\.

1. For **Integration Type**, choose one of the following:
   + Choose **Lambda Function** only if your API will be integrated with an AWS Lambda function that you have already created in this account or in another account\.

     To create a new Lambda function in AWS Lambda, to set a resource permission on the Lambda function, or to perform any other Lambda service actions, choose **AWS Service** instead\.
   + Choose **HTTP** if your API will be integrated with an existing HTTP endpoint\. For more information, see [Set up HTTP integrations in API Gateway](setup-http-integrations.md)\.
   + Choose **Mock** if you want to generate API responses from API Gateway directly, without the need for an integration backend\. For more information, see [Set up mock integrations in API Gateway](how-to-mock-integration.md)\.
   + Choose **AWS Service** if your API will be integrated with an AWS service\.
   + Choose **VPC Link** if your API will use a `VpcLink` as a private integration endpoint\. For more information, see [Set up API Gateway private integrations](set-up-private-integration.md)\.

1. If you chose **Lambda Function**, do the following:

   1. For **Lambda Region**, choose the Region identifier that corresponds to the Region where you created the Lambda function\. For example, if you created the Lambda function in the US East \(N\. Virginia\) Region, choose **us\-east\-1**\. For a list of Region names and identifiers, see [AWS Lambda](https://docs.aws.amazon.com/general/latest/gr/rande.html#lambda_region) in the *Amazon Web Services General Reference*\.

   1. For **Use Lambda Proxy integration**, choose the check box if you intend to use [Lambda proxy integration](set-up-lambda-proxy-integrations.md#api-gateway-create-api-as-simple-proxy) or [cross\-account Lambda proxy integration](apigateway-cross-account-lambda-integrations.md)\.

   1. For **Lambda Function**, specify the function in one of the following ways:
      + If your Lambda function is in the same account, start typing the function name and then choose the function from the dropdown list\.
**Note**  
The function name can optionally include its alias or version specification, as in `HelloWorld`, `HelloWorld:1`, or `HelloWorld:alpha`\.
      + If the function is in a different account, enter the ARN for the function\.

   1. Choose **Invoke with caller credentials** if you want API Gateway to invoke your Lambda function using credentials received in the incoming request\.

   1. For **Execution role**, enter the ARN of the Lambda invocation role that enables API Gateway to invoke your Lambda functions\.

   1. To use the default timeout value of 29 seconds, leave **Use Default Timeout** check box selected\. To set a custom timeout, clear the check box, and enter a timeout value between `50` and `29000` milliseconds\.

   1. Choose **Save**\.

1. If you chose **HTTP**, follow the instructions in step 4 of [Set up an API integration request using the API Gateway console](how-to-method-settings-console.md)\.

1. If you chose **Mock**, proceed to the **Request Templates** step\.

1. If you chose **AWS Service**, follow the instructions in step 6 of [Set up an API integration request using the API Gateway console](how-to-method-settings-console.md)\.

1. If you chose **VPC Link**, do the following:

   1. For **Use Proxy integration**, choose the check box if you want requests to be proxied to your `VPCLink`'s endpoint\.

   1. From the **VPC Link** dropdown list, choose `[Use Stage Variables]` and enter `${stageVariables.vpcLinkId}` in the text box below the list\.

      We will define the `vpcLinkId` stage variable after deploying the API to a stage and set its value to the ID of the `VpcLink`\.

   1. For **HTTP method**, choose the HTTP method type that most closely matches the method in the HTTP backend\.

   1. For **Endpoint URL**, enter the URL of the HTTP backend you want this integration to use\.

   1. To use the default timeout value of 29 seconds, leave **Use Default Timeout** selection checked\. To set a custom timeout, uncheck the box, and enter a timeout value between `50` and `29000` milliseconds\.

1. Under **Request Templates**, do the following:

   1. For **Template Selection Expression**, choose the pencil icon and replace the word `template` with a template selection expression\. This is an expression that API Gateway looks for in the message payload\. If it is found, it is evaluated, and the result is a template key value that is used to select the data mapping template to be applied to the data in the message payload\.

     For information about template selection expressions, see [Template selection expressions](websocket-api-data-transformations.md#apigateway-websocket-api-template-selection-expressions)\.

## Set up an integration request using the AWS CLI<a name="apigateway-websocket-api-integration-request-using-awscli"></a>

You can set up an integration request for a route in a WebSocket API by using the AWS CLI as in the following example, which creates a mock integration:

1. Create a file named `integration-params.json`, with the following contents:

   ```
   {"PassthroughBehavior": "WHEN_NO_MATCH", "TimeoutInMillis": 29000, "ConnectionType": "INTERNET", "RequestTemplates": {"application/json": "{\"statusCode\":200}"}, "IntegrationType": "MOCK"}
   ```

1. Run the [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateIntegration](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateIntegration) command as shown in the following example:

   ```
   aws apigatewayv2 --region us-east-1 create-integration --api-id aabbccddee --cli-input-json file://integration-params.json
   ```

Following is sample output for this example:

```
{
    "PassthroughBehavior": "WHEN_NO_MATCH",
    "TimeoutInMillis": 29000,
    "ConnectionType": "INTERNET",
    "IntegrationResponseSelectionExpression": "${response.statuscode}",
    "RequestTemplates": {
        "application/json": "{\"statusCode\":200}"
    },
    "IntegrationId": "0abcdef",
    "IntegrationType": "MOCK"
}
```

Alternatively, you can set up an integration request for a proxy integration by using the AWS CLI as in the following example:

1. Create a Lambda function in the Lambda console and give it a basic Lambda execution role\.

1. Execute the [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateIntegration](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateIntegration) command as in the following example:

   ```
   aws apigatewayv2 create-integration --api-id aabbccddee --integration-type AWS_PROXY --integration-method POST --integration-uri arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123412341234:function:simpleproxy-echo-e2e/invocations
   ```

Following is sample output for this example:

```
{
    "PassthroughBehavior": "WHEN_NO_MATCH",
    "IntegrationMethod": "POST",
    "TimeoutInMillis": 29000,
    "ConnectionType": "INTERNET",
    "IntegrationUri": "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123412341234:function:simpleproxy-echo-e2e/invocations",
    "IntegrationId": "abcdefg",
    "IntegrationType": "AWS_PROXY"
}
```