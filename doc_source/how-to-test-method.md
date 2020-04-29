# Use the API Gateway console to test a REST API method<a name="how-to-test-method"></a>

Use the API Gateway console to test a REST API method\.

**Topics**
+ [Prerequisites](#how-to-test-method-prerequisites)
+ [Test a method with the API Gateway console](#how-to-test-method-console)

## Prerequisites<a name="how-to-test-method-prerequisites"></a>
+ You must specify the settings for the methods you want to test\. Follow the instructions in [Set up REST API methods in API Gateway](how-to-method-settings.md)\.

## Test a method with the API Gateway console<a name="how-to-test-method-console"></a>

**Important**  
Testing methods with the API Gateway console may result in changes to resources that cannot be undone\. Testing a method with the API Gateway console is the same as calling the method outside of the API Gateway console\. For example, if you use the API Gateway console to call a method that deletes an API's resources, if the method call is successful, the API's resources will be deleted\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose a REST API\.

1. In the **Resources** pane, choose the method you want to test\.

1. In the **Method Execution** pane, in the **Client** box, choose **TEST**\. Type values in any of the displayed boxes \(such as **Query Strings**, **Headers**, and **Request Body**\)\. The console includes these values in the method request in default application/json form\.

   For additional options you may need to specify, contact the API owner\.

1. Choose **Test**\. The following information will be displayed:
   + **Request** is the resource's path that was called for the method\.
   + **Status** is the response's HTTP status code\.
   + **Latency** is the time between the receipt of the request from the caller and the returned response\.
   + **Response Body** is the HTTP response body\.
   + **Response Headers** are the HTTP response headers\.
**Tip**  
Depending on the mapping, the HTTP status code, response body, and response headers may be different from those sent from the Lambda function, HTTP proxy, or AWS service proxy\.
   + **Logs** are the simulated Amazon CloudWatch Logs entries that would have been written if this method were called outside of the API Gateway console\.
**Note**  
Although the CloudWatch Logs entries are simulated, the results of the method call are real\.

 In addition to using the API Gateway console, you can use AWS CLI or an AWS SDK for API Gateway to test invoking a method\. To do so using AWS CLI, see [test\-invoke\-method](https://docs.aws.amazon.com/cli/latest/reference/apigateway/test-invoke-method.html)\. 