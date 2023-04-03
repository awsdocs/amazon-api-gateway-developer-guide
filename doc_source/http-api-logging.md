# Configuring logging for an HTTP API<a name="http-api-logging"></a>

## <a name="http-api-logging.intro"></a>

You can turn on logging to write logs to CloudWatch Logs\. You can use [logging variables](http-api-logging-variables.md) to customize the content of your logs\.

To turn on logging for an HTTP API, you must do the following\.

1. Ensure that your user has the required permissions to activate logging\.

1. Create a CloudWatch Logs log group\.

1. Provide the ARN of the CloudWatch Logs log group for a stage of your API\.

## Permissions to activate logging<a name="http-api-logging.permissions"></a>

To turn on logging for an API, your user must have the following permissions\.

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

## Create a log group and activate logging for HTTP APIs<a name="http-api-enable-logging"></a>

You can create a log group and activate access logging using the AWS Management Console or the AWS CLI\.

------
#### [ AWS Management Console ]

1.  Create a log group\. 

   To learn how to create a log group using the console, see [Create a Log Group in Amazon CloudWatch Logs User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html)\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose an HTTP API\.

1. Under the **Monitor** tab in the primary navigation panel, choose **Logging**\.

1. Select a stage to activate logging and choose **Select**\. 

1. Choose **Edit** to activate access logging\. 

1. Turn on **Access logging**, enter a CloudWatch Logs, and select a log format\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

The following AWS CLI command creates a log group\.

```
aws logs create-log-group --log-group-name my-log-group
```

You need the Amazon Resource Name \(ARN\) for your log group to turn on logging\. The ARN format is arn:aws:logs:*region*:*account\-id*:log\-group:*log\-group\-name*\.

The following AWS CLI command turns on logging for the `$default` stage of an HTTP API\.

```
aws apigatewayv2 update-stage --api-id abcdef \
    --stage-name '$default' \
    --access-log-settings '{"DestinationArn": "arn:aws:logs:region:account-id:log-group:log-group-name", "Format": "$context.identity.sourceIp - - [$context.requestTime] \"$context.httpMethod $context.routeKey $context.protocol\" $context.status $context.responseLength $context.requestId"}'
```

------

## Example log formats<a name="http-api-enable-logging.examples"></a>

Examples of some common access log formats are available in the API Gateway console and are listed as follows\.
+ `CLF` \([Common Log Format](https://httpd.apache.org/docs/current/logs.html#common)\):

  ```
  $context.identity.sourceIp - - [$context.requestTime] "$context.httpMethod $context.routeKey $context.protocol" $context.status $context.responseLength $context.requestId
  ```
+  `JSON`: 

  ```
  { "requestId":"$context.requestId", "ip": "$context.identity.sourceIp", "requestTime":"$context.requestTime", "httpMethod":"$context.httpMethod","routeKey":"$context.routeKey", "status":"$context.status","protocol":"$context.protocol", "responseLength":"$context.responseLength" }
  ```
+ `XML`: 

  ```
  <request id="$context.requestId"> <ip>$context.identity.sourceIp</ip> <requestTime>$context.requestTime</requestTime> <httpMethod>$context.httpMethod</httpMethod> <routeKey>$context.routeKey</routeKey> <status>$context.status</status> <protocol>$context.protocol</protocol> <responseLength>$context.responseLength</responseLength> </request>
  ```
+ `CSV` \(comma\-separated values\):

  ```
  $context.identity.sourceIp,$context.requestTime,$context.httpMethod,$context.routeKey,$context.protocol,$context.status,$context.responseLength,$context.requestId
  ```