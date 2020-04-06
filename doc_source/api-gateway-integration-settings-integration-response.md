# Set up an integration response in API Gateway<a name="api-gateway-integration-settings-integration-response"></a>

 For a non\-proxy integration, you must set up at least one integration response, and make it the default response, to pass the result returned from the backend to the client\. You can choose to pass through the result as\-is or to transform the integration response data to the method response data if the two have different formats\. 

For a proxy integration, API Gateway automatically passes the backend output to the client as an HTTP response\. You do not set either an integration response or a method response\.

To set up an integration response, you perform the following required and optional tasks:

1.  Specify an HTTP status code of a method response to which the integration response data is mapped\. This is required\.

1.  Define a regular expression to select backend output to be represented by this integration response\. If you leave this empty, the response is the default response that is used to catch any response not yet configured\.

1.  If needed, declare mappings consisting of key\-value pairs to map specified integration response parameters to given method response parameters\.

1. If needed, add body\-mapping templates to transform given integration response payloads into specified method response payloads\.

1.  If needed, specify how to handle type conversion for a binary payload\.

An integration response is an HTTP response encapsulating the backend response\. For an HTTP endpoint, the backend response is an HTTP response\. The integration response status code can take the backend\-returned status code, and the integration response body is the backend\-returned payload\. For a Lambda endpoint, the backend response is the output returned from the Lambda function\. With the Lambda integration, the Lambda function output is returned as a `200 OK` response\. The payload can contain the result as JSON data, including a JSON string or a JSON object, or an error message as a JSON object\. You can assign a regular expression to the [selectionPattern](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/#selectionPattern) property to map an error response to an appropriate HTTP error response\. For more information about the Lambda function error response, see [Handle Lambda errors in API Gateway](handle-errors-in-lambda-integration.md)\. With the Lambda proxy integration, the Lambda function must return output of the following format:

```
{
    statusCode: "...",            // a valid HTTP status code
    headers: { 
        custom-header: "..."      // any API-specific custom header
    },
    body: "...",                  // a JSON string.
    isBase64Encoded:  true|false  // for binary support
}
```

There is no need to map the Lambda function response to its proper HTTP response\.

To return the result to the client, set up the integration response to pass the endpoint response through as\-is to the corresponding method response\. Or you can map the endpoint response data to the method response data\. The response data that can be mapped includes the response status code, response header parameters, and response body\. If no method response is defined for the returned status code, API Gateway returns a 500 error\. For more information, see [Working with models and mapping templates](models-mappings.md)\.