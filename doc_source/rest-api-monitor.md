# Monitoring REST APIs<a name="rest-api-monitor"></a>

In this section, you can learn how to monitor your API by using CloudWatch metrics, CloudWatch Logs, Kinesis Data Firehose, and AWS X\-Ray\. By combining CloudWatch execution logs and CloudWatch metrics, you can log errors and execution traces, and monitor your API's performance\. You might also want to log API calls to Kinesis Data Firehose\. You can also use AWS X\-Ray to trace calls through the downstream services that make up your API\.

**Note**  
API Gateway might not generate logs and metrics in the following cases:  
413 Request Entity Too Large errors
Excessive 429 Too Many Requests errors
400 series errors from requests sent to a custom domain that has no API mapping
500 series errors caused by internal failures

**Topics**
+ [Monitoring REST API execution with Amazon CloudWatch metrics](monitoring-cloudwatch.md)
+ [Setting up CloudWatch logging for a REST API in API Gateway](set-up-logging.md)
+ [Logging API calls to Kinesis Data Firehose](apigateway-logging-to-kinesis.md)
+ [Tracing user requests to REST APIs using X\-Ray](apigateway-xray.md)