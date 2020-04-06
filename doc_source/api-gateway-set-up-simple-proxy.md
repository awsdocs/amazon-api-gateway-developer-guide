# Set up a proxy integration with a proxy resource<a name="api-gateway-set-up-simple-proxy"></a>

To set up a proxy integration in an API Gateway API with a proxy resource, you perform the following tasks: 
+ Create a proxy resource with a greedy path variable of `{proxy+}`\. 
+ Set the `ANY` method on the proxy resource\.
+ Integrate the resource and method with a backend using the HTTP or Lambda integration type\.

**Note**  
Greedy path variables, `ANY` methods, and proxy integration types are independent features, although they are commonly used together\. You can configure a specific HTTP method on a greedy resource or apply non\-proxy integration types to a proxy resource\.

API Gateway enacts certain restrictions and limitations when handling methods with either a Lambda proxy integration or an HTTP proxy integration\. For details, see [Amazon API Gateway important notes](api-gateway-known-issues.md)\. 

**Note**  
 When using proxy integration with a passthrough, API Gateway returns the default `Content-Type:application/json` header if the content type of a payload is unspecified\. 

A proxy resource is most powerful when it is integrated with a backend using either HTTP proxy integration or Lambda proxy [integration](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration)\.

## HTTP proxy integration with a proxy resource<a name="api-gateway-proxy-integration-types"></a>

The HTTP proxy integration, designated by `HTTP_PROXY` in the API Gateway REST API, is for integrating a method request with a backend HTTP endpoint\. With this integration type, API Gateway simply passes the entire request and response between the frontend and the backend, subject to certain [restrictions and limitations](api-gateway-known-issues.md)\.

**Note**  
HTTP proxy integration supports multi\-valued headers and query strings\.

When applying the HTTP proxy integration to a proxy resource, you can set up your API to expose a portion or an entire endpoint hierarchy of the HTTP backend with a single integration setup\. For example, suppose the backend of the website is organized into multiple branches of tree nodes off the root node \(`/site`\) as: `/site/a0/a1/.../aN`, `/site/b0/b1/.../bM`, etc\. If you integrate the `ANY` method on a proxy resource of `/api/{proxy+}` with the backend endpoints with URL paths of `/site/{proxy}`, a single integration request can support any HTTP operations \(GET, POST, etc\.\) on any of `[a0, a1, ..., aN, b0, b1, ...bM, ...]`\. If you apply a proxy integration to a specific HTTP method, for example, `GET`, instead, the resulting integration request works with the specified \(that is, `GET`\) operations on any of those backend nodes\. 

## Lambda proxy integration with a proxy resource<a name="lambda-proxy-integration-with-proxy-resource"></a>

The Lambda proxy integration, designated by `AWS_PROXY` in the API Gateway REST API, is for integrating a method request with a Lambda function in the backend\. With this integration type, API Gateway applies a default mapping template to send the entire request to the Lambda function and transforms the output from the Lambda function to HTTP responses\. 

Similarly, you can apply the Lambda proxy integration to a proxy resource of `/api/{proxy+}` to set up a single integration to have a backend Lambda function react individually to changes in any of the API resources under `/api`\. 