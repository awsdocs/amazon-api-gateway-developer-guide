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

The following AWS CLI command creates a log group\.

```
aws logs create-log-group --log-group-name my-log-group
```

You need the Amazon Resource Name \(ARN\) for your log group to enable logging\. The ARN format is arn:aws:logs:*region*:*account\-id*:log\-group:*log\-group\-name*\.

## Enabling logging for a stage<a name="http-api-enable-logging.console"></a>

The following AWS CLI command enables logging for the `$default` stage of an HTTP API\.

```
aws apigatewayv2 update-stage --api-id abcdef \
    --stage-name '$default' \
    --access-log-settings '{"DestinationArn": "arn:aws:logs:region:account-id:log-group:log-group-name", "Format": "$context.identity.sourceIp - - [$context.requestTime] \"$context.httpMethod $context.routeKey $context.protocol\" $context.status $context.responseLength $context.requestId"}'
```

## Example log formats<a name="http-api-enable-logging.examples"></a>

Examples of some common access log formats are available in the API Gateway console and are listed as follows\.
+ `CLF` \([Common Log Format](https://httpd.apache.org/docs/1.3/logs.html#common)\):

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