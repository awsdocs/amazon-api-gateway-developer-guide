# Creating an API in Amazon API Gateway<a name="how-to-create-api"></a>

 In Amazon API Gateway, you build an API with a collection of programmable entities known as API Gateway [resources](https://docs.aws.amazon.com/apigateway/api-reference/resource/)\. For example, you use a [RestApi](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/) resource to represent an API that can contain a collection of [Resource](https://docs.aws.amazon.com/apigateway/api-reference/resource/resource/) entities\. Each `Resource` entity can in turn have one or more [Method](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/) resources\. Expressed in the request parameters and body, a `Method` defines the application programming interface for the client to access the exposed `Resource` and represents an incoming request submitted by the client\. You then create an [https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) resource to integrate the `Method` with a backend endpoint, also known as the integration endpoint, by forwarding the incoming request to a specified integration endpoint URI\. If necessary, you transform request parameters or body to meet the backend requirements\. For responses, you can create a [MethodResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/) resource to represent a request response received by the client and you create an [IntegrationResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/) resource to represent the request response that is returned by the backend\. You can configure the integration response to transform the backend response data before returning the data to the client or to pass the backend response as\-is to the client\. 

To help your customers understand your API, you can also provide documentation for the API, as part of the API creation or after the API is created\. To enable this, add a `[DocumentationPart](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/)` resource for a supported API entity\. 

To control how clients call an API, use [IAM permissions](permissions.md), a [Lambda authorizer](apigateway-use-lambda-authorizer.md), or an [Amazon Cognito user pool](apigateway-integrate-with-cognito.md)\. To meter the use of your API, set up `[usage plans](https://docs.aws.amazon.com/apigateway/api-reference/resource/usage-plan/)` to throttle API requests\. You can enable these when creating or updating the API\.

 You can perform these and other tasks by using the API Gateway console, the API Gateway REST API, the AWS CLI, or one of the AWS SDKs\. We discuss how to perform these tasks next\. 

**Topics**
+ [Choose an Endpoint Type to Set up an API Gateway API](#api-gateway-api-endpoint-types)
+ [Initialize API Setup in API Gateway](create-api-resources-methods.md)
+ [Set up API Methods in API Gateway](how-to-method-settings.md)
+ [Set up API Integrations in API Gateway](how-to-integration-settings.md)
+ [Set up Gateway Responses to Customize Error Responses](customize-gateway-responses.md)
+ [Set up API Gateway Request and Response Data Mappings](mappings.md)
+ [Support Binary Payloads in API Gateway](api-gateway-payload-encodings.md)
+ [Enable Payload Compression for an API](api-gateway-gzip-compression-decompression.md)
+ [Enable Request Validation in API Gateway](api-gateway-method-request-validation.md)
+ [Update and Maintain an API in Amazon API Gateway](updating-api.md)
+ [Import an API into API Gateway](api-gateway-import-api.md)

## Choose an Endpoint Type to Set up an API Gateway API<a name="api-gateway-api-endpoint-types"></a>

An [API endpoint](api-gateway-basic-concept.md) refers to a host name of the API\. the API endpoint can be edge\-optimized or regional, depending on where the majority of your API traffic originates from\. You choose a specific endpoint type when creating an API\. 

An edge\-optimized API endpoint optimizes access to an API by geographically distributed clients through an Amazon CloudFront distribution\. API requests are routed to the nearest CloudFront Point of Presence \(POP\) \. By default, an API is created with the edge\-optimized endpoint\.

A regional API is intended for clients in the same region\. When a client running on an EC2 instance calls an API in the same region, or when an API is intended to serve a small number of clients with high demands, a regional API reduces connection overhead\. 

For an edge\-optimized API, you create a custom domain name that applies across all the regions\. For a regional API, you create a custom domain name that is specific to the API hosting region\. It is possible that a regional API deployed in different regions can have the same custom domain name\. 