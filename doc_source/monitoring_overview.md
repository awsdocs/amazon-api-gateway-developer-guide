# Trace API Management and Invocation<a name="monitoring_overview"></a>

**Topics**
+ [Log API management calls to Amazon API Gateway Using AWS CloudTrail](cloudtrail.md)
+ [Monitor API execution with Amazon CloudWatch](monitoring-cloudwatch.md)

For API execution, API Gateway automatically reports to Amazon CloudWatch your API's execution metrics on the API\- and stage\-levels, provided that your account has an IAM role with permissions to write logs into CloudWatch configured\. 

You can also opt in for API Gateway to send to CloudWatch method\-level metrics using the [API Gateway console](stages.md#how-to-stage-settings) or calling the [API Gateway REST API](http://docs.aws.amazon.com/apigateway/api-reference/)or one of its SDKs\. Based on these metrics, you can set CloudWatch custom alarms for troubleshooting any performance issues of your APIs\. 

The CloudWatch metrics include statistics about caching, latency and detected errors\. You can inspect the CloudWatch logs to troubleshoot your API implementation or execution using the API dashboard in the API Gateway console or using the CloudWatch console\. For more information about CloudWatch, see the [Amazon CloudWatch User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)\.

For API management operations, you can create AWS CloudTrail trails to log events involved in the API Gateway REST API calls\. You can create the trails in the CloudTrail console\. 

You can use the logs to troubleshoot API creation, deployment and updates\. You can also use Amazon CloudWatch to monitor the CloudTrail logs\. To learn more about CloudTrail, see the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)\. 

**Note**  
CloudTrail logs API Gateway REST API calls an API developer or owner made against the `apigateway` component, whereas CloudWatch logs API calls an API customer or client made against the `execute-api` component of API Gateway\.