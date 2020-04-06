# Setting up REST API integrations<a name="how-to-integration-settings"></a>

 After setting up an API method, you must integrate it with an endpoint in the backend\. A backend endpoint is also referred to as an integration endpoint and can be a Lambda function, an HTTP webpage, or an AWS service action\. 

As with the API method, the API integration has an integration request and an integration response\. An integration request encapsulates an HTTP request received by the backend\. It might or might not differ from the method request submitted by the client\. An integration response is an HTTP response encapsulating the output returned by the backend\.

Setting up an integration request involves the following: configuring how to pass client\-submitted method requests to the backend; configuring how to transform the request data, if necessary, to the integration request data; and specifying which Lambda function to call, specifying which HTTP server to forward the incoming request to, or specifying the AWS service action to invoke\. 

Setting up an integration response \(applicable to non\-proxy integrations only\) involves the following: configuring how to pass the backend\-returned result to a method response of a given status code, configuring how to transform specified integration response parameters to preconfigured method response parameters, and configuring how to map the integration response body to the method response body according to the specified body\-mapping templates\. 

Programmatically, an integration request is encapsulated by the [https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) resource and an integration response by the [https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/) resource of API Gateway\. 

To set up an integration request, you create an [https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) resource and use it to configure the integration endpoint URL\. You then set the IAM permissions to access the backend, and specify mappings to transform the incoming request data before passing it to the backend\. To set up an integration response for non\-proxy integration, you create an [https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/) resource and use it to set its target method response\. You then configure how to map backend output to the method response\.

**Topics**
+ [Set up an integration request in API Gateway](api-gateway-integration-settings-integration-request.md)
+ [Set up an integration response in API Gateway](api-gateway-integration-settings-integration-response.md)
+ [Set up Lambda integrations in API Gateway](set-up-lambda-integrations.md)
+ [Set up HTTP integrations in API Gateway](setup-http-integrations.md)
+ [Set up API Gateway private integrations](set-up-private-integration.md)
+ [Set up mock integrations in API Gateway](how-to-mock-integration.md)