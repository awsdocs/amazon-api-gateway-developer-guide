# Logging and monitoring in Amazon API Gateway<a name="security-monitoring"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of API Gateway and your AWS solutions\. You should collect monitoring data from all of the parts of your AWS solution so that you can more easily debug a multi\-point failure if one occurs\. AWS provides several tools for monitoring your API Gateway resources and responding to potential incidents:

**Amazon CloudWatch Logs**  
To help debug issues related to request execution or client access to your API, you can enable CloudWatch Logs to log API calls\. For more information, see [Setting up CloudWatch logging for a REST API in API Gateway](set-up-logging.md)\.

**Amazon CloudWatch Alarms**  
Using CloudWatch alarms, you watch a single metric over a time period that you specify\. If the metric exceeds a given threshold, a notification is sent to an Amazon Simple Notification Service topic or AWS Auto Scaling policy\. CloudWatch alarms do not invoke actions when a metric is in a particular state\. Rather the state must have changed and been maintained for a specified number of periods\. For more information, see [Monitoring REST API execution with Amazon CloudWatch metrics](monitoring-cloudwatch.md)\.

**Access Logging to Kinesis Data Firehose**  
To help debug issues related to client access to your API, you can enable Kinesis Data Firehose to log API calls\. For more information, see [Logging API calls to Kinesis Data Firehose](apigateway-logging-to-kinesis.md)\.

**AWS CloudTrail**  
CloudTrail provides a record of actions taken by a user, role, or an AWS service in API Gateway\. Using the information collected by CloudTrail, you can determine the request that was made to API Gateway, the IP address from which the request was made, who made the request, when it was made, and additional details\. For more information, see [Logging calls to Amazon API Gateway APIs with AWS CloudTrail](cloudtrail.md)\. 

**AWS X\-Ray**  
X\-Ray is an AWS service that gathers data about the requests that your application serves, and uses it to construct a service map that you can use to identify issues with your application and opportunities for optimization\. For more information, see [Setting up AWS X\-Ray with API Gateway REST APIs](apigateway-enabling-xray.md)\. 

**AWS Config**  
AWS Config provides a detailed view of the configuration of AWS resources in your account\. You can see how resources are related, get a history of configuration changes, and see how relationships and configurations change over time\. You can use AWS Config to define rules that evaluate resource configurations for data compliance\. AWS Config rules represent the ideal configuration settings for your API Gateway resources\. If a resource violates a rule and is flagged as noncompliant, AWS Config can alert you using an Amazon Simple Notification Service \(Amazon SNS\) topic\. For details, see [Monitoring API Gateway API configuration with AWS Config](apigateway-config.md)\. 