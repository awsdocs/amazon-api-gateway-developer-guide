# Configuring logging for an HTTP API<a name="http-api-logging"></a>

## <a name="http-api-logging.intro"></a>

You can enable logging to write logs to CloudWatch Logs\. You can use [logging variables](http-api-logging-variables.md) to customize the content of your logs\.

To enable logging for an HTTP API, you must do the following\.

1. Ensure that your IAM user has the required permissions to enable logging\.

1. Create a CloudWatch Logs log group\.

1. Provide the ARN of the CloudWatch Logs log group for a stage of your API\.

## Permissions to enable logging<a name="http-api-logging.permissions"></a>

To enable logging for an API, your IAM user must have the following permissions\.

**Example**  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams",
                "logs:GetLogEvents",
                "logs:FilterLogEvents"
            ],
            "Resource": "arn:aws:logs:us-east-2:123456789012:log-group:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogDelivery",
                "logs:PutResourcePolicy",
                "logs:UpdateLogDelivery",
                "logs:DeleteLogDelivery",
                "logs:CreateLogGroup",
                "logs:DescribeResourcePolicies",
                "logs:GetLogDelivery",
                "logs:ListLogDeliveries"
            ],
            "Resource": "*"
        }
    ]
}
```

## Creating a log group<a name="http-api-logging.create-log-group"></a>

**To create a log group by using the AWS Management Console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Log groups**\.

1. Choose **Create log group**\.

1. Enter a log group name, and then choose **Create**\.

1. Note the Amazon Resource Name \(ARN\) for your log group\. The ARN format is arn:aws:logs:*region*: *account\-id*:log\-group:*log\-group\-name*\. You need the log group ARN to enable access logging for your HTTP API\.

## Enabling logging for a stage<a name="http-api-enable-logging.console"></a>

**To enable logging by using the AWS Management Console**

1. Open the [API Gateway console](https://console.aws.amazon.com/apigateway)\.

1. Choose an API\.

1. Under **Monitor**, choose **Logging**\.

1. Choose the stage for which you want to enable logging\.

1. Choose **Edit**\.

1. Use the **Access logging** toggle to enable access logging\.

1. For **Log destination**, enter the ARN of a CloudWatch Logs log group\. The ARN format is `arn:aws:logs:region:account-id:log-group:log-group-name`\. 

1. Enter a log format in **Log format**\. You can choose **CLF**, **JSON**, **XML**, or **CSV** to use one of the provided examples as a guide\.

1. Choose **Save**\.