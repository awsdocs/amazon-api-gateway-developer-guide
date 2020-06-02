# Working with metrics for HTTP APIs<a name="http-api-metrics"></a>

You can monitor API execution by using CloudWatch, which collects and processes raw data from API Gateway into readable, near\-real\-time metrics\. These statistics are recorded for a period of 15 months so you can access historical information and gain a better perspective on how your web application or service is performing\. By default, API Gateway metric data is automatically sent to CloudWatch in one\-minute periods\. For more information, see [What Is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

The following metrics are supported for HTTP APIs\. You can also enable detailed metrics to write route\-level metrics to Amazon CloudWatch\.


| Metric | Description | 
| --- | --- | 
| 4xx | The number of client\-side errors captured in a given period\. | 
| 5xx | The number of server\-side errors captured in a given period\. | 
| Count | The total number API requests in a given period\. | 
| IntegrationLatency | The time between when API Gateway relays a request to the backend and when it receives a response from the backend\. | 
| Latency | The time between when API Gateway receives a request from a client and when it returns a response to the client\. The latency includes the integration latency and other API Gateway overhead\. | 
| DataProcessed | The amount of data processed in bytes\. | 

You can use the dimensions in the following table to filter API Gateway metrics\.


| Dimension | Description | 
| --- | --- | 
| ApiId | Filters API Gateway metrics for an API with the specified API ID\. | 
| ApiId, Stage | Filters API Gateway metrics for an API stage with the specified API ID and stage ID\. | 
| ApiId, Stage, Route |  Filters API Gateway metrics for an API method with the specified API ID, stage ID, and route ID\. API Gateway will not send these metrics unless you have explicitly enabled detailed CloudWatch metrics\. You can do this by calling the UpdateStage action of the API Gateway V2 REST API to update the metricsEnabled property to `true`\. Enabling such metrics will incur additional charges to your account\. For pricing information, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\.  | 