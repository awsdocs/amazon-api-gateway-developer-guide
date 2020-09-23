# Setting up CloudWatch logging for a REST API in API Gateway<a name="set-up-logging"></a>

 To help debug issues related to request execution or client access to your API, you can enable Amazon CloudWatch Logs to log API calls\. For more information about CloudWatch, see [Monitoring REST API execution with Amazon CloudWatch metrics](monitoring-cloudwatch.md)\.

## CloudWatch log formats for API Gateway<a name="apigateway-cloudwatch-log-formats"></a>

 There are two types of API logging in CloudWatch: execution logging and access logging\. In execution logging, API Gateway manages the CloudWatch Logs\. The process includes creating log groups and log streams, and reporting to the log streams any caller's requests and responses\. 

The logged data includes errors or execution traces \(such as request or response parameter values or payloads\), data used by Lambda authorizers \(formerly known as custom authorizers\), whether API keys are required, whether usage plans are enabled, and so on\. 

When you deploy an API, API Gateway creates a log group and log streams under the log group\. The log group is named following the `API-Gateway-Execution-Logs_{rest-api-id}/{stage_name}` format\. Within each log group, the logs are further divided into log streams, which are ordered by **Last Event Time** as logged data is reported\. 

In access logging, you, as an API developer, want to log who has accessed your API and how the caller accessed the API\. You can create your own log group or choose an existing log group that could be managed by API Gateway\. To specify the access details, you select [`$context`](api-gateway-mapping-template-reference.md#context-variable-reference) variables \(expressed in a format of your choosing\) and choose a log group as the destination\. To preserve the uniqueness of each log, the access log format must include `$context.requestId`\. 

**Note**  
Only `$context` variables are supported \(not `$input`, and so on\)\.

Choose a log format that is also adopted by your analytic backend, such as [Common Log Format](https://httpd.apache.org/docs/1.3/logs.html#common) \(CLF\), JSON, XML, or CSV\. You can then feed the access logs to it directly to have your metrics computed and rendered\. To define the log format, set the log group ARN on the [accessLogSettings/destinationArn](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#destinationArn) property on the [stage](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/)\. You can obtain a log group ARN in the CloudWatch console, provided that the **ARN** column is selected for display\. To define the access log format, set a chosen format on the [accessLogSetting/format](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#format) property on the [stage](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/)\. 

Examples of some commonly used access log formats are shown in the API Gateway console and are listed as follows\.
+ `CLF` \([Common Log Format](https://httpd.apache.org/docs/1.3/logs.html#common)\):

  ```
  $context.identity.sourceIp $context.identity.caller  \
  $context.identity.user [$context.requestTime] \
  "$context.httpMethod $context.resourcePath $context.protocol" \
  $context.status $context.responseLength $context.requestId
  ```

  The continuation characters \(`\`\) are meant as a visual aid\. The log format must be a single line\. You can add a newline character \(`\n`\) at the end of the log format to include a newline at the end of each log entry\.
+  `JSON`: 

  ```
  { "requestId":"$context.requestId", \
    "ip": "$context.identity.sourceIp", \
    "caller":"$context.identity.caller", \
    "user":"$context.identity.user", \
    "requestTime":"$context.requestTime", \
    "httpMethod":"$context.httpMethod", \
    "resourcePath":"$context.resourcePath", \
    "status":"$context.status", \
    "protocol":"$context.protocol", \
    "responseLength":"$context.responseLength" \
  }
  ```

  The continuation characters \(`\`\) are meant as a visual aid\. The log format must be a single line\. You can add a newline character \(`\n`\) at the end of the log format to include a newline at the end of each log entry\.
+ `XML`: 

  ```
  <request id="$context.requestId"> \
      <ip>$context.identity.sourceIp</ip> \
      <caller>$context.identity.caller</caller> \
      <user>$context.identity.user</user> \
      <requestTime>$context.requestTime</requestTime> \
      <httpMethod>$context.httpMethod</httpMethod> \
      <resourcePath>$context.resourcePath</resourcePath> \
      <status>$context.status</status> \
      <protocol>$context.protocol</protocol> \
      <responseLength>$context.responseLength</responseLength> \
  </request>
  ```

  The continuation characters \(`\`\) are meant as a visual aid\. The log format must be a single line\. You can add a newline character \(`\n`\) at the end of the log format to include a newline at the end of each log entry\.
+ `CSV` \(comma\-separated values\):

  ```
  $context.identity.sourceIp,$context.identity.caller,\
  $context.identity.user,$context.requestTime,$context.httpMethod,\
  $context.resourcePath,$context.protocol,$context.status,\
  $context.responseLength,$context.requestId
  ```

  The continuation characters \(`\`\) are meant as a visual aid\. The log format must be a single line\. You can add a newline character \(`\n`\) at the end of the log format to include a newline at the end of each log entry\.

## Permissions for CloudWatch logging<a name="set-up-access-logging-permissions"></a>

To enable CloudWatch Logs, you must grant API Gateway permission to read and write logs to CloudWatch for your account\. The `AmazonAPIGatewayPushToCloudWatchLogs` managed policy \(with an ARN of `arn:aws:iam::aws:policy/service-role/AmazonAPIGatewayPushToCloudWatchLogs`\) has all the required permissions:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams",
                "logs:PutLogEvents",
                "logs:GetLogEvents",
                "logs:FilterLogEvents"
            ],
            "Resource": "*"
        }
    ]
}
```

**Note**  
API Gateway calls AWS Security Token Service in order to assume the IAM role, so make sure that AWS STS is enabled for the Region\. For more information, see [Managing AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html)\.

To grant these permissions to your account, create an IAM role with `apigateway.amazonaws.com` as its trusted entity, attach the preceding policy to the IAM role, and set the IAM role ARN on the [cloudWatchRoleArn](https://docs.aws.amazon.com/apigateway/api-reference/resource/account/#cloudWatchRoleArn) property on your [Account](https://docs.aws.amazon.com/apigateway/api-reference/resource/account/)\. You must set the [cloudWatchRoleArn](https://docs.aws.amazon.com/apigateway/api-reference/resource/account/#cloudWatchRoleArn) property separately for each AWS Region in which you want to enable CloudWatch Logs\.

If you receive an error when setting the IAM role ARN, check your AWS Security Token Service account settings to make sure that AWS STS is enabled in the Region that you're using\. For more information about enabling AWS STS, see [Managing AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html#sts-regions-activate-deactivate) in the *IAM User Guide*\.

## Set up CloudWatch API logging using the API Gateway console<a name="set-up-access-logging-using-console"></a>

To set up CloudWatch API logging, you must have deployed the API to a stage\. You must also have configured [an appropriate CloudWatch Logs role](#set-up-access-logging-permissions) ARN for your account\. 

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose a REST API\.

1. Choose **Settings** from the primary navigation panel and enter an ARN of an IAM role with appropriate permissions in **CloudWatch log role ARN**\. You need to do this once\. 

1.  Do one of the following:

   1.  Choose an existing API and then choose a stage\.

   1.  Create an API and deploy it to a stage\.

1.  Choose **Logs/Tracing** in the **Stage Editor**\.

1. To enable execution logging:

   1. Choose **Enable CloudWatch Logs** under **CloudWatch Settings**\.

   1. Choose **Error** or **Info** from the dropdown menu\.

   1. If desired, choose **Log full requests/responses data** to log the full API requests and responses\.
**Warning**  
This can be useful to troubleshoot APIs, but can result in logging sensitive data\. We recommend that you don't enable `Log full requests/responses data` for production APIs\.

   1. If desired, choose **Enable Detailed CloudWatch Metrics**\.

   For more information about CloudWatch metrics, see [Monitoring REST API execution with Amazon CloudWatch metrics](monitoring-cloudwatch.md)\.

1. To enable access logging:

   1. Choose **Enable Access Logging** under **Custom Access Logging**\.

   1. Enter the ARN of a log group in **Access Log Destination ARN**\. The ARN format is `arn:aws:logs:{region}:{account-id}:log-group:log-group-name`\.

   1. Enter a log format in **Log Format**\. You can choose **CLF**, **JSON**, **XML**, or **CSV** to use one of the provided examples as a guide\.

1. Choose **Save Changes**\.

**Note**  
You can enable execution logging and access logging independent of each other\.

API Gateway is now ready to log requests to your API\. You don't need to redeploy the API when you update the stage settings, logs, or stage variables\. 