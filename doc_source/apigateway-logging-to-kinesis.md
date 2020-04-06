# Logging API calls to Kinesis Data Firehose<a name="apigateway-logging-to-kinesis"></a>

To help debug issues related to client access to your API, you can log API calls to Amazon Kinesis Data Firehose\. For more information about Kinesis Data Firehose, see [What Is Amazon Kinesis Data Firehose?](https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html)\.

For access logging, you can only enable CloudWatch or Kinesis Data Firehose—you can't enable both\. However, you can enable CloudWatch for execution logging and Kinesis Data Firehose for access logging\.

**Topics**
+ [Kinesis Data Firehose log formats for API Gateway](#apigateway-kinesis-log-formats)
+ [Permissions for Kinesis Data Firehose logging](#set-up-kinesis-access-logging-permissions)
+ [Set up Kinesis Data Firehose access logging by using the API Gateway console](#set-up-kinesis-access-logging-using-console)

## Kinesis Data Firehose log formats for API Gateway<a name="apigateway-kinesis-log-formats"></a>

Kinesis Data Firehose logging uses the same format as [CloudWatch logging](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-logging.html)\.

## Permissions for Kinesis Data Firehose logging<a name="set-up-kinesis-access-logging-permissions"></a>

When Kinesis Data Firehose access logging is enabled on a stage, API Gateway creates a service\-linked role in your account if the role doesn't exist already\. The role is named `AWSServiceRoleForAPIGateway` and has the `APIGatewayServiceRolePolicy` managed policy attached to it\. For more information about service\-linked roles, see [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html)\.

**Note**  
The name of your Kinesis Data Firehose delivery stream must be `amazon-apigateway-{your-delivery-stream-name}`\.

## Set up Kinesis Data Firehose access logging by using the API Gateway console<a name="set-up-kinesis-access-logging-using-console"></a>

To set up API logging, you must have deployed the API to a stage\. You must also have created a Kinesis Data Firehose delivery stream\. 

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1.  Do one of the following:

   1.  Choose an existing API and then choose a stage\.

   1.  Create an API and deploy it to a stage\.

1.  Choose **Logs/Tracing** in the **Stage Editor**\.

1. To enable access logging to a Kinesis Data Firehose delivery stream:

   1. Choose **Enable Access Logging** under **Custom Access Logging**\.

   1. Enter the ARN of a Kinesis Data Firehose delivery stream in **Access Log Destination ARN**\. The ARN format is `arn:aws:firehose:{region}:{account-id}:deliverystream/amazon-apigateway-{your-delivery-stream-name}`\.
**Note**  
The name of your delivery stream must be `amazon-apigateway-{your-delivery-stream-name}`\.

   1. Enter a log format in **Log Format**\. You can choose **CLF**, **JSON**, **XML**, or **CSV** to use one of the provided examples as a guide\.

1. Choose **Save Changes**\.

API Gateway is now ready to log requests to your API to Kinesis Data Firehose\. You don't need to redeploy the API when you update the stage settings, logs, or stage variables\. 