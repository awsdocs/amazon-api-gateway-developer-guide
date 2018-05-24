# Monitor API execution with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor API execution using CloudWatch, which collects and processes raw data from API Gateway into readable, near real\-time metrics\. These statistics are recorded for a period of two weeks, so that you can access historical information and gain a better perspective on how your web application or service is performing\. By default, API Gateway metric data is automatically sent to CloudWatch in one\-minute periods\. For more information, see [What Are Amazon CloudWatch, Amazon CloudWatch Events, and Amazon CloudWatch Logs?](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

The metrics reported by API Gateway provide information that you can analyze in different ways\. The list below shows some common uses for the metrics\. These are suggestions to get you started, not a comprehensive list\.
+ Monitor the **IntegrationLatency** metrics to measure the responsiveness of the backend\.
+ Monitor the **Latency** metrics to measure the overall responsiveness of your API calls\.
+ Monitor the **CacheHitCount** and **CacheMissCount** metrics to optimize cache capacities to achieve a desired performance\.

**Topics**
+ [Amazon API Gateway Dimensions and Metrics](api-gateway-metrics-and-dimensions.md)
+ [View CloudWatch Metrics with the API Dashboard in API Gateway](how-to-api-dashboard.md)
+ [View API Gateway Metrics in the CloudWatch Console](metrics_dimensions_view_in_cloud_watch.md)
+ [View API Gateway Log Events in the CloudWatch Console](view-cloudwatch-log-events-in-cloudwatch-console.md)
+ [Monitoring Tools in AWS](monitoring_automated_manual.md)