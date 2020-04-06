# Security best practices in Amazon API Gateway<a name="security-best-practices"></a>

API Gateway provides a number of security features to consider as you develop and implement your own security policies\. The following best practices are general guidelines and don’t represent a complete security solution\. Because these best practices might not be appropriate or sufficient for your environment, treat them as helpful considerations rather than prescriptions\.

**Implement least privilege access**  
Use IAM policies to implement least privilege access for creating, reading, updating, or deleting API Gateway APIs\. To learn more, see [Identity and access management for Amazon API Gateway](security-iam.md)\. API Gateway offers several options to control access to APIs that you create\. To learn more, see [Controlling and managing access to a REST API in API Gateway](apigateway-control-access-to-api.md), [Controlling and managing access to a WebSocket API in API Gateway](apigateway-websocket-api-control-access.md), and [Controlling access to HTTP APIs with JWT authorizers](http-api-jwt-authorizer.md)\.

**Implement logging**  
Use CloudWatch Logs or Amazon Kinesis Data Firehose to log requests to your APIs\. To learn more, see [Monitoring REST APIs](rest-api-monitor.md), [Configuring logging for a WebSocket API](websocket-api-logging.md), and [Configuring logging for an HTTP API](http-api-logging.md)\.

**Implement Amazon CloudWatch alarms**  
Using CloudWatch alarms, you watch a single metric over a time period that you specify\. If the metric exceeds a given threshold, a notification is sent to an Amazon Simple Notification Service topic or AWS Auto Scaling policy\. CloudWatch alarms do not invoke actions when a metric is in a particular state\. Rather, the state must have changed and been maintained for a specified number of periods\. For more information, see [Monitoring REST API execution with Amazon CloudWatch metrics](monitoring-cloudwatch.md)\.

**Enable AWS CloudTrail**  
CloudTrail provides a record of actions taken by a user, role, or an AWS service in API Gateway\. Using the information collected by CloudTrail, you can determine the request that was made to API Gateway, the IP address from which the request was made, who made the request, when it was made, and additional details\. For more information, see [Logging calls to Amazon API Gateway APIs with AWS CloudTrail](cloudtrail.md)\. 

**Enable AWS Config**  
AWS Config provides a detailed view of the configuration of AWS resources in your account\. You can see how resources are related, get a history of configuration changes, and see how relationships and configurations change over time\. You can use AWS Config to define rules that evaluate resource configurations for data compliance\. AWS Config rules represent the ideal configuration settings for your API Gateway resources\. If a resource violates a rule and is flagged as noncompliant, AWS Config can alert you using an Amazon Simple Notification Service \(Amazon SNS\) topic\. For details, see [Monitoring API Gateway API configuration with AWS Config](apigateway-config.md)\. 