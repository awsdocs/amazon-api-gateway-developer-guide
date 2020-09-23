# Use Postman to call a REST API<a name="how-to-use-postman-to-call-api"></a>

The [Postman](http://www.postman.com) app is a convenient tool to test a REST API in API Gateway\. The following instructions walk you through the essential steps of using the Postman app to call an API\. For more information, see the Postman [help](https://www.postman.com/docs/)\.

1. Launch Postman\.

1. Enter the endpoint URL of a request in the address bar and choose the appropriate HTTP method from the drop\-down list to the left of the address bar\.

1. If required, choose the **Authorization** tab\. Choose **AWS Signature** for the authorization **Type**\. Enter your AWS IAM user's access key ID in the **AccessKey** input field\. Enter your IAM user secret key in **SecretKey**\. Specify an appropriate AWS region that matches the region specified in the invocation URL\. Enter **execute\-api** in **Service Name**\.

1. Choose the **Headers** tab\. Optionally, delete any existing headers\. This can clear any stale settings that may cause errors\. Add any required custom headers\. For example, if API keys are enabled, you can set the **x\-api\-key:*\{api\_key\}*** name/value pair here\.

1. Choose **Send** to submit the request and receive a response\.

For an example of using Postman, see [Call an API with API Gateway Lambda authorizers](call-api-with-api-gateway-lambda-authorization.md)\.