# Troubleshooting issues with HTTP API Lambda integrations<a name="http-api-troubleshooting-lambda"></a>

The following provides troubleshooting advice for errors and issues that you might encounter when using [AWS Lambda integrations](http-api-develop-integrations-lambda.md) with HTTP APIs\.

## Issue: My API with a Lambda integration returns `{"message":"Internal Server Error"}`<a name="http-api-troubleshooting-lambda-internal-server-error"></a>

To troubleshoot the internal server error, add the `$context.integrationErrorMessage` [logging variable](http-api-logging-variables.md) to your log format, and view your HTTP API's logs\. To achieve this, do the following:

**To create a log group by using the AWS Management Console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Log groups**\.

1. Choose **Create log group**\.

1. Enter a log group name, and then choose **Create**\.

1. Note the Amazon Resource Name \(ARN\) for your log group\. The ARN format is arn:aws:logs:*region*: *account\-id*:log\-group:*log\-group\-name*\. You need the log group ARN to enable access logging for your HTTP API\.

**To add the `$context.integrationErrorMessage` logging variable**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your HTTP API\.

1. Under **Metrics**, choose **Logging**\.

1. Select a stage of your API\.

1. Choose **Edit**, and then enable access logging\.

1. For **Log format**, choose **CLF**\.

1. Add `$context.integrationErrorMessage` to the end of the log format\.

1. Choose **Save**\.

**To view your API's logs**

1. Generate logs\. Use a browser or `curl` to invoke your API\.

   ```
   $curl https://api-id.execute-api.us-west-2.amazonaws.com/route
   ```

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your HTTP API\.

1. Under **Metrics**, choose **Logging**\.

1. Select the stage of your API for which you enabled logging\.

1. Choose **View logs in CloudWatch**\.

1. Choose the latest log stream to view your HTTP API's logs\.

1. Your log entry should look similar to the following:  
![\[CloudWatch Logs log entry showing the integration error message from Lambda.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/troubleshoot-http-api-logs.png)

Because we added `$context.integrationErrorMessage` to the log format, we see an error message in our logs that summarizes the problem\. 

Your logs might include a different error message that indicates that there's a problem with your Lambda function code\. In that case, check your Lambda function code, and verify that your Lambda function returns a response in the [required format](http-api-develop-integrations-lambda.md#http-api-develop-integrations-lambda.response)\.

In this case, the logs show that API Gateway didn't have the required permissions to invoke the Lambda function\.

When you create a Lambda integration in the API Gateway console, API Gateway automatically configures permissions to invoke the Lambda function\. When you create a Lambda integration by using the AWS CLI, AWS CloudFormation, or an SDK, you must grant permissions for API Gateway to invoke the function\. The following AWS CLI command grants permissions for the `$default` stage and `$default` route of an HTTP API to invoke a Lambda function\.

```
aws lambda add-permission \
    --function-name my-function \
    --statement-id apigateway-invoke-permissions \ 
    --action lambda:InvokeFunction \
    --principal apigateway.amazonaws.com 
    --source-arn "arn:aws:execute-api:us-west-2:123456789012:api-id/\$default/\$default"
```

[Confirm the function policy](https://docs.aws.amazon.com/lambda/latest/dg/access-control-resource-based.html) in the **Permissions** tab of the Lambda console\.

Try invoking your API again\. You should see your Lambda function's response\.