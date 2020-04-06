# Monitoring tools in AWS<a name="monitoring_automated_manual"></a>

AWS provides various tools that you can use to monitor API Gateway\. You can configure some of these tools to do the monitoring for you automatically, while other tools require manual intervention\. We recommend that you automate monitoring tasks as much as possible\.

## Automated monitoring tools in AWS<a name="monitoring_automated_tools"></a>

You can use the following automated monitoring tools to watch API Gateway and report when something is wrong:
+ **Amazon CloudWatch Alarms** – Watch a single metric over a time period that you specify, and perform one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon Simple Notification Service \(Amazon SNS\) topic or Amazon EC2 Auto Scaling policy\. CloudWatch alarms do not invoke actions simply because they are in a particular state; the state must have changed and been maintained for a specified number of periods\. For more information, see [Monitoring REST API execution with Amazon CloudWatch metrics](monitoring-cloudwatch.md)\.
+ **Amazon CloudWatch Logs** – Monitor, store, and access your log files from AWS CloudTrail or other sources\. For more information, see [Monitoring Log Files](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatchLogs.html) in the *Amazon CloudWatch User Guide*\.
+ **Amazon CloudWatch Events** – Match events and route them to one or more target functions or streams to make changes, capture state information, and take corrective action\. For more information, see [What is Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatchEvents.html) in the *Amazon CloudWatch User Guide*\.
+ **AWS CloudTrail Log Monitoring** – Share log files between accounts, monitor CloudTrail log files in real time by sending them to CloudWatch Logs, write log processing applications in Java, and validate that your log files have not changed after delivery by CloudTrail\. For more information, see [Working with CloudTrail Log Files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-working-with-log-files.html) in the *AWS CloudTrail User Guide*\. 

## Manual monitoring tools<a name="monitoring_manual_tools"></a>

Another important part of monitoring API Gateway involves manually monitoring those items that the CloudWatch alarms don't cover\. The API Gateway, CloudWatch, and other AWS console dashboards provide an at\-a\-glance view of the state of your AWS environment\. We recommend that you also check the log files on API execution\.
+ API Gateway dashboard shows the following statistics for a given API stage during a specified period of time:
  + **API Calls**
  + **Cache Hit**, only when API caching is enabled\.
  + **Cache Miss**, only when API caching is enabled\.
  + **Latency**
  + **Integration Latency**
  + **4XX Error**
  + **5XX Error**
+ The CloudWatch home page shows:
  + Current alarms and status
  + Graphs of alarms and resources
  + Service health status

  In addition, you can use CloudWatch to do the following: 
  + Create [customized dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CloudWatch_Dashboards.html) to monitor the services you care about
  + Graph metric data to troubleshoot issues and discover trends
  + Search and browse all your AWS resource metrics
  + Create and edit alarms to be notified of problems

## Creating CloudWatch alarms to monitor API Gateway<a name="creating_alarms"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period you specify, and performs one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon SNS topic or Auto Scaling policy\. Alarms invoke actions for sustained state changes only\. CloudWatch alarms do not invoke actions simply because they are in a particular state; the state must have changed and been maintained for a specified number of periods\.