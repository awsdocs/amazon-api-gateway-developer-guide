# Monitoring your HTTP API<a name="http-api-monitor"></a>

You can use CloudWatch metrics and CloudWatch Logs to monitor HTTP APIs\. By combining logs and metrics, you can log errors and monitor your API's performance\.

**Note**  
API Gateway might not generate logs and metrics in the following cases:  
413 Request Entity Too Large errors
Excessive 429 Too Many Requests errors
400 series errors from requests sent to a custom domain that has no API mapping
500 series errors caused by internal failures

**Topics**
+ [Working with metrics for HTTP APIs](http-api-metrics.md)
+ [Configuring logging for an HTTP API](http-api-logging.md)