# Set up Lambda integrations in API Gateway<a name="set-up-lambda-integrations"></a>

 You can integrate an API method with a Lambda function using Lambda proxy integration or Lambda non\-proxy \(custom\) integration\. 

In Lambda proxy integration, the setup is simple\. If your API does not require content encoding or caching, you only need to set the integration's HTTP method to POST, the integration endpoint URI to the ARN of the Lambda function invocation action of a specific Lambda function, and the credential to an IAM role with permissions to allow API Gateway to call the Lambda function on your behalf\. 

In Lambda non\-proxy integration, in addition to the proxy integration setup steps, you also specify how the incoming request data is mapped to the integration request and how the resulting integration response data is mapped to the method response\. 

**Topics**
+ [Set up Lambda proxy integrations in API Gateway](set-up-lambda-proxy-integrations.md)
+ [Set up Lambda custom integrations in API Gateway](set-up-lambda-custom-integrations.md)
+ [Set up asynchronous invocation of the backend Lambda function](set-up-lambda-integration-async.md)
+ [Handle Lambda errors in API Gateway](handle-errors-in-lambda-integration.md)