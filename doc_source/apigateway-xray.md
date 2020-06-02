# Tracing user requests to REST APIs using X\-Ray<a name="apigateway-xray"></a>

You can use [AWS X\-Ray](https://docs.aws.amazon.com/xray/latest/devguide/xray-services-apigateway.html) to trace and analyze user requests as they travel through your Amazon API Gateway REST APIs to the underlying services\. API Gateway supports X\-Ray tracing for all API Gateway REST API endpoint types: Regional, edge\-optimized, and private\. You can use X\-Ray with Amazon API Gateway in all AWS Regions where X\-Ray is available\.

Because X\-Ray gives you an end\-to\-end view of an entire request, you can analyze latencies in your APIs and their backend services\. You can use an X\-Ray service map to view the latency of an entire request and that of the downstream services that are integrated with X\-Ray\. You can also configure sampling rules to tell X\-Ray which requests to record and at what sampling rates, according to criteria that you specify\. 

If you call an API Gateway API from a service that's already being traced, API Gateway passes the trace through, even if X\-Ray tracing isn't enabled on the API\.

You can enable X\-Ray for an API stage by using the API Gateway console, or by using the API Gateway API or CLI\.

**Topics**
+ [Setting up AWS X\-Ray with API Gateway REST APIs](apigateway-enabling-xray.md)
+ [Using AWS X\-Ray service maps and trace views with API Gateway](apigateway-using-xray-maps.md)
+ [Configuring AWS X\-Ray sampling rules for API Gateway APIs](apigateway-configuring-xray-sampling-rules.md)
+ [Understanding AWS X\-Ray traces for Amazon API Gateway APIs](apigateway-understanding-xray-traces.md)