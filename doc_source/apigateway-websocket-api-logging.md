# Monitor WebSocket API Execution with CloudWatch<a name="apigateway-websocket-api-logging"></a>

You can use [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) metrics and logs to monitor the execution of WebSocket APIs\. The configuration is similar to that used for REST APIs\.

For instructions on how to set up CloudWatch access and execution logging, see [Set up CloudWatch API Logging Using the API Gateway Console](set-up-logging.md#set-up-access-logging-using-console)\.

When you specify the **Log Format**, you can choose which context variables to log\. Here is an example of a JSON\-formatted list of context variables for a WebSocket API:

```
{ 
            "apiId" : "$context.apiId", 
            "routeKey" : "$context.routeKey", 
            "authorizer" : "$context.authorizer", 
            "messageId" : "$context.messageId", 
            "integrationLatency" : "$context.integrationLatency", 
            "eventType" : "$context.eventType", 
            "error" : "$context.error", 
            "extendedRequestId" : "$context.extendedRequestId", 
            "requestTime" : "$context.requestTime", 
            "stage" : "$context.stage", 
            "connectedAt" : "$context.connectedAt", 
            "requestTimeEpoch" : "$context.requestTimeEpoch", 
            "requestId" : "$context.requestId", 
            "connectionId" : "$context.connectionId"
}
```

You can find WebSocket\-specific context variables here: [API Gateway WebSocket API Mapping Template Reference](apigateway-websocket-api-mapping-template-reference.md)

The following metrics are supported for WebSocket APIs:


| Metric | Description | 
| --- | --- | 
| ConnectCount | Number of messages sent to the $connect route integration\. | 
| MessageCount | Number of messages sent to the WebSocket API, either from or to the client\. | 
| IntegrationError | Number of requests that return a 4XX/5XX response from the integration\. | 
| ClientError | Number of requests that have a 4XX response returned by API Gateway before the integration is invoked\. | 
| ExecutionError | Errors that occurred when calling the integration\. | 
| IntegrationLatency | The time difference between API Gateway sending the request to the integration and API Gateway receiving the response from the integration\. Suppressed for callbacks and mock integrations\. | 

You can use the dimensions in the following table to filter API Gateway metrics\.


| Dimension | Description | 
| --- | --- | 
| ApiId | Filters API Gateway metrics for an API with the specified API ID\. | 
| ApiId, Stage | Filters API Gateway metrics for an API stage with the specified API ID and stage ID\. | 
| ApiId, Stage, Route |  Filters API Gateway metrics for an API method with the specified API ID, stage ID, and route ID\. API Gateway will not send these metrics unless you have explicitly enabled detailed CloudWatch metrics\. You can do this by calling the UpdateStage action of the API Gateway V2 REST API to update the metricsEnabled property to true\. Enabling such metrics will incur additional charges to your account\. For pricing information, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\.  | 