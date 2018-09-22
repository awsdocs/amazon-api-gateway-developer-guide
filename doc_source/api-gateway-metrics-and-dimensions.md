# Amazon API Gateway Dimensions and Metrics<a name="api-gateway-metrics-and-dimensions"></a>

The metrics and dimensions that API Gateway sends to Amazon CloudWatch are listed below\. For more information, see [Monitor API execution with Amazon CloudWatch](monitoring-cloudwatch.md)\. 

## API Gateway Metrics<a name="api-gateway-metrics"></a>

Amazon API Gateway sends metric data to CloudWatch every minute\.

The `AWS/ApiGateway` namespace includes the following metrics\.


| Metric | Description | 
| --- | --- | 
|  4XXError |  The number of client\-side errors captured in a specified period\.  The `Sum` statistic represents this metric, namely, the total count of the 4XXError errors in the given period\. The `Average` statistic represents the 4XXError error rate, namely, the total count of the 4XXError errors divided by the total number of requests during the period\. The denominator corresponds to the Count metric \(below\)\.  Unit: Count  | 
|  5XXError  |  The number of server\-side errors captured in a given period\. The `Sum` statistic represents this metric, namely, the total count of the 5XXError errors in the given period\. The `Average` statistic represents the 5XXError error rate, namely, the total count of the 5XXError errors divided by the total number of requests during the period\. The denominator corresponds to the Count metric \(below\)\.  Unit: Count  | 
|  CacheHitCount  |  The number of requests served from the API cache in a given period\. The `Sum` statistic represents this metric, namely, the total count of the cache hits in the specified period\. The `Average` statistic represents the cache hit rate, namely, the total count of the cache hits divided by the total number of requests during the period\. The denominator corresponds to the Count metric \(below\)\.  Unit: Count  | 
|  CacheMissCount  |  The number of requests served from the back end in a given period, when API caching is enabled\. The `Sum` statistic represents this metric, namely, the total count of the cache misses in the specified period\. The `Average` statistic represents the cache miss rate, namely, the total count of the cache hits divided by the total number of requests during the period\. The denominator corresponds to the Count metric \(below\)\.  Unit: Count  | 
|  Count  |  The total number API requests in a given period\.  The `SampleCount` statistic represents this metric\. Unit: Count  | 
|  IntegrationLatency  |  The time between when API Gateway relays a request to the back end and when it receives a response from the back end\. Unit: Millisecond  | 
|  Latency  |  The time between when API Gateway receives a request from a client and when it returns a response to the client\. The latency includes the integration latency and other API Gateway overhead\. Unit: Millisecond  | 

## Dimensions for Metrics<a name="api-gateway-metricdimensions"></a>

You can use the dimensions in the following table to filter API Gateway metrics\.


| Dimension | Description | 
| --- | --- | 
|  ApiName  |  Filters API Gateway metrics for an API of the specified API name\.  | 
|  ApiName, Method, Resource, Stage  |  Filters API Gateway metrics for an API method of the specified API, stage, resource, and method\. API Gateway will not send such metrics unless you have explicitly enabled detailed CloudWatch metrics\. You can do this in the console by selecting **Enable CloudWatch Metrics** under a stage **Settings** tab\. Alternatively, you can call the [stage:update](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/) action of the API Gateway REST API to update the `metricsEnabled` property to `true`\.  Enabling such metrics will incur additional charges to your account\. For pricing information, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\.  | 
|  ApiName, Stage  |  Filters API Gateway metrics for an API stage of the specified API and stage\.  | 