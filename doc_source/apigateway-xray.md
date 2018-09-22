# Trace API Gateway API Execution with AWS X\-Ray<a name="apigateway-xray"></a>

You can use [AWS X\-Ray](https://docs.aws.amazon.com/xray/latest/devguide/xray-services-apigateway.html) to trace and analyze user requests as they travel through your Amazon API Gateway APIs to the underlying services\. API Gateway supports AWS X\-Ray tracing for all API Gateway endpoint types: regional, edge\-optimized, and private\. You can use AWS X\-Ray with Amazon API Gateway in all regions where X\-Ray is available\.

X\-Ray gives you an end\-to\-end view of an entire request, so you can analyze latencies in your APIs and their backend services\. You can use an X\-Ray service map to view the latency of an entire request and that of the downstream services that are integrated with X\-Ray\. And you can configure sampling rules to tell X\-Ray which requests to record, at what sampling rates, according to criteria that you specify\. If you call an API Gateway API from a service that's already being traced, API Gateway passes the trace through, even if X\-Ray tracing is not enabled on the API\.

You can enable X\-Ray for an API stage by using the API Gateway management console, or by using the API Gateway API or CLI\.

**Topics**
+ [Setting Up AWS X\-Ray with API Gateway](apigateway-enabling-xray.md)
+ [Using AWS X\-Ray Service Maps and Trace Views with API Gateway](apigateway-using-xray-maps.md)
+ [Configuring AWS X\-Ray Sampling Rules for API Gateway APIs](apigateway-configuring-xray-sampling-rules.md)
+ [Understanding AWS X\-Ray Traces for Amazon API Gateway APIs](apigateway-understanding-xray-traces.md)