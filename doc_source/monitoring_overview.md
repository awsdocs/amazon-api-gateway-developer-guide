# Tracing, Logging, and Monitoring an API Gateway API<a name="monitoring_overview"></a>

[AWS X\-Ray](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html), [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html), and [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) are tools that Amazon API Gateway developers can use to trace, log, and monitor API execution and management operations\.

AWS X\-Ray is an AWS service that allows you to trace latency issues with your Amazon API Gateway APIs\. X\-Ray collects metadata from the API Gateway service and any downstream services that make up your API\. X\-Ray uses this metadata to generate a detailed service graph that illustrates latency spikes and other issues that impact the performance of your API\.

Amazon CloudWatch logs API execution operations, which are calls that an API customer or client application makes against the API Gateway `execute-api` component\. CloudWatch metrics include statistics about caching, latency and detected errors\. You can inspect CloudWatch logs to troubleshoot your API implementation or execution using the API dashboard in the API Gateway console or using the CloudWatch console\.

AWS CloudTrail logs API Gateway API management operations, which are REST API calls that an API developer or owner makes against the API Gateway `apigateway` component\. You can use CloudTrail logs to troubleshoot API creation, deployment and updates\. You can also use Amazon CloudWatch to monitor CloudTrail logs\.

**Topics**
+ [Trace API Gateway API Execution with AWS X\-Ray](apigateway-xray.md)
+ [Log Amazon API Gateway API Calls with AWS CloudTrail](cloudtrail.md)
+ [Monitor API execution with Amazon CloudWatch](monitoring-cloudwatch.md)