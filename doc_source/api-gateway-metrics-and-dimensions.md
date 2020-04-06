# Amazon API Gateway dimensions and metrics<a name="api-gateway-metrics-and-dimensions"></a>

The metrics and dimensions that API Gateway sends to Amazon CloudWatch are listed below\. For more information, see [Monitoring REST API execution with Amazon CloudWatch metrics](monitoring-cloudwatch.md)\. 

## API Gateway metrics<a name="api-gateway-metrics"></a>

Amazon API Gateway sends metric data to CloudWatch every minute\.

The `AWS/ApiGateway` namespace includes the following metrics\.


| Metric | Description | 
| --- | --- | 
|  4XXError |  The number of client\-side errors captured in a given period\. The `Sum` statistic represents this metric, namely, the total count of the 4XXError errors in the given period\. The `Average` statistic represents the 4XXError error rate, namely, the total count of the 4XXError errors divided by the total number of requests during the period\. The denominator corresponds to the Count metric \(below\)\. Unit: Count  | 
|  5XXError  |  The number of server\-side errors captured in a given period\. The `Sum` statistic represents this metric, namely, the total count of the 5XXError errors in the given period\. The `Average` statistic represents the 5XXError error rate, namely, the total count of the 5XXError errors divided by the total number of requests during the period\. The denominator corresponds to the Count metric \(below\)\. Unit: Count  | 
|  CacheHitCount  |  The number of requests served from the API cache in a given period\. The `Sum` statistic represents this metric, namely, the total count of the cache hits in the given period\. The `Average` statistic represents the cache hit rate, namely, the total count of the cache hits divided by the total number of requests during the period\. The denominator corresponds to the Count metric \(below\)\. Unit: Count  | 
|  CacheMissCount  |  The number of requests served from the backend in a given period, when API caching is enabled\. The `Sum` statistic represents this metric, namely, the total count of the cache misses in the given period\. The `Average` statistic represents the cache miss rate, namely, the total count of the cache misses divided by the total number of requests during the period\. The denominator corresponds to the Count metric \(below\)\. Unit: Count  | 
|  Count  |  The total number API requests in a given period\. The `SampleCount` statistic represents this metric\. Unit: Count  | 
|  IntegrationLatency  |  The time between when API Gateway relays a request to the backend and when it receives a response from the backend\. Unit: Millisecond  | 
|  Latency  |  The time between when API Gateway receives a request from a client and when it returns a response to the client\. The latency includes the integration latency and other API Gateway overhead\. Unit: Millisecond  | 

## Dimensions for metrics<a name="api-gateway-metricdimensions"></a>

You can use the dimensions in the following table to filter API Gateway metrics\.

**Note**  
API Gateway removes non\-ASCII characters from the ApiName dimension before sending metrics to CloudWatch\. If the APIName contains no ASCII characters, the API ID is used as the ApiName\.


| Dimension | Description | 
| --- | --- | 
|  ApiName  |  Filters API Gateway metrics for the REST API with the specified API name\.  | 
|  ApiName, Method, Resource, Stage  |  Filters API Gateway metrics for the API method with the specified API name, stage, resource, and method\. API Gateway will not send these metrics unless you have explicitly enabled detailed CloudWatch metrics\. You can do this in the console by selecting **Enable CloudWatch Metrics** under a stage **Settings** tab\. Alternatively, you can call the [update\-stage](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-stage.html) AWS CLI command to update the `metricsEnabled` property to `true`\. Enabling these metrics will incur additional charges to your account\. For pricing information, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\.  | 
|  ApiName, Stage  |  Filters API Gateway metrics for the API stage resource with the specified API name and stage\.  | 