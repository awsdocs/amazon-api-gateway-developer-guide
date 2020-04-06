# Using AWS X\-Ray service maps and trace views with API Gateway<a name="apigateway-using-xray-maps"></a>

In this section you can find detailed information on how to use [AWS X\-Ray](https://docs.aws.amazon.com/xray/latest/devguide/xray-services-apigateway.html) service maps and trace views with API Gateway\.

For detailed information about service maps and trace views, and how to interpret them, see [AWS X\-Ray Console](https://docs.aws.amazon.com/xray/latest/devguide/xray-console.html)\.

**Topics**
+ [Example X\-Ray service map](#apigateway-using-xray-maps-active)
+ [Example X\-Ray trace view](#apigateway-using-xray-trace-view-active)

## Example X\-Ray service map<a name="apigateway-using-xray-maps-active"></a>

AWS X\-Ray service maps show information about your API and all of its downstream services\. When X\-Ray is enabled for an API stage in API Gateway, you'll see a node in the service map containing information about the overall time spent in the API Gateway service\. You can get detailed information about the response status and a histogram of the API response time for the selected timeframe\. For APIs integrating with AWS services such as AWS Lambda and Amazon DynamoDB, you will see more nodes providing performance metrics related to those services\. There will be a service map for each API stage\.

The following example shows a service map for the `test` stage of an API called `xray`\. This API has a Lambda integration with a Lambda authorizer function and a Lambda backend function\. The nodes represent the API Gateway service, the Lambda service, and the two Lambda functions\.

For a detailed explanation of service map structure, see [Viewing the Service Map](https://docs.aws.amazon.com/xray/latest/devguide/xray-console.html#xray-console-servicemap)\.

![\[Service map example of an API Gateway API stage\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-xray-servicemap-2.png)

From the service map, you can zoom in to see a trace view of your API stage\. The trace will display in\-depth information regarding your API, represented as segments and subsegments\. For example, the trace for the service map shown above would include segments for the Lambda service and Lambda function\. For more information, see [Lambda as an AWS X\-Ray Trace](https://docs.aws.amazon.com/xray/latest/devguide/using-x-ray.html#lambda-request)\.

If you choose a node or edge on an X\-Ray service map, the X\-Ray console shows a latency distribution histogram\. You can use a latency histogram to see how long it takes for a service to complete its requests\. Following is a histogram of the API Gateway stage named `xray/test` in the previous service map\. For a detailed explanation of latency distribution histograms, see [Using Latency Histograms in the AWS X\-Ray Console](https://docs.aws.amazon.com/xray/latest/devguide/xray-console-histograms.html)\.

![\[X-Ray histogram of an API Gateway API stage\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-xray-histogram-1.png)

## Example X\-Ray trace view<a name="apigateway-using-xray-trace-view-active"></a>

The following diagram shows a trace view generated for the example API described above, with a Lambda backend function and a Lambda authorizer function\. A successful API method request is shown with a response code of 200\.

For a detailed explanation of trace views, see [Viewing Traces](https://docs.aws.amazon.com/xray/latest/devguide/xray-console.html#xray-console-traces)\.

![\[API Gateway with active tracing enabled\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-xray-traceview-1.png)