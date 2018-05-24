# Next Step<a name="api-gateway-create-api-from-example-next"></a>

Through the example API, we became familiar with the basic workflow for creating an API in API Gateway\. The process is summarized as follows:

1. Create an API as a [RestApi](http://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/) resource in your AWS account\.

1. Add a [Resource](http://docs.aws.amazon.com/apigateway/api-reference/resource/resource/) resource to the Resources hierarchy of the newly created API\.

1. Create a [Method](http://docs.aws.amazon.com/apigateway/api-reference/resource/method/) resource for the `Resource`\. The API method represents a programming interface between a client and API Gateway\.

1. Set up the integration of the method with a backend endpoint\. The integration represents an interface between the API Gateway and a backend endpoint\.

 When a user accesses the backend service through the API, the client submits an HTTP request to API Gateway\. This submission puts the request through the `Method Request` and then `Integration Request` before reaching the backend\. The backend then returns a response to API Gateway\. The response then passes from `Integration Response` to `Method Response` before the client receives the response\. The `MOCK` integrations demonstrated in this example API are perhaps the simplest cases of pre\-processing and post\-processing of requests or responses by API Gateway\. We cover other cases elsewhere in this guide\. 

 Next, we move on to learning how to build and test a more nimble and powerful API with [proxy integrations](set-up-lambda-proxy-integrations.md#api-gateway-create-api-as-simple-proxy)\. 