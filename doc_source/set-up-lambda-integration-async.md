# Set up Asynchronous Invocation of the Backend Lambda Function<a name="set-up-lambda-integration-async"></a>

In Lambda proxy integration and Lambda non\-proxy \(custom\) integration, the backend Lambda function is invoked synchronously by default\. This is the desired behavior for most REST API operations\. Some applications, however, require work to be performed asynchronously \(as a batch operation or a long\-latency operation\), typically by a separate backend component\. In this case, the backend Lambda function is invoked asynchronously, and the front\-end REST API method doesn't return the result\.

You can configure the Lambda function to be invoked asynchronously by specifying `'Event'` as the [Lambda invocation type](https://docs.aws.amazon.com/lambda/latest/dg/invocation-options.html)\. This is done as follows:

**Configure Lambda asynchronous invocation in the API Gateway console**

1. In **Integration Request**, add an `X-Amz-Invocation-Type` header\.

1. In **Method Request**, add an `InvocationType` header and map it to the `X-Amz-Invocation-Type` header in the **Integration Request** with either a static value of `'Event'` or the header mapping expression of `method.request.header.InvocationType`\. For the latter, the client must include the `InvocationType:Event` header when making a request to the API method\.