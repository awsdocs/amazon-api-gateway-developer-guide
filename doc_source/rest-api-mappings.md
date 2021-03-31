# Working with API mappings for REST APIs<a name="rest-api-mappings"></a>

You use API mappings to connect API stages to a custom domain name\. After you create a domain name and configure DNS records, you use API mappings to send traffic to your APIs through your custom domain name\.

An API mapping specifies an API, a stage, and optionally a path to use for the mapping\. For example, you can map the `production` stage of an API to `https://api.example.com/orders`\.

You can map HTTP and REST API stages to the same custom domain name\.

Before you create an API mapping, you must have an API, a stage, and a custom domain name\. To learn more about creating a custom domain name, see [Setting up a regional custom domain name in API Gateway](apigateway-regional-api-custom-domain-create.md)\.

## Routing API requests<a name="rest-api-mappings-evalutation"></a>

You can configure API mappings with multiple levels, for example `orders/v1/items` and `orders/v2/items`\.

**Note**  
To configure API mappings with multiple levels, your custom domain name must be regional and use the TLS 1\.2 security policy\.

API Gateway routes requests to the API mapping that has the longest matching path\. API Gateway considers only the paths configured for API mappings, and not API routes, to select the API to invoke\. If no path matches the request, API Gateway sends the request to the API that you've mapped to the empty path `(none)`\.

For example, consider a custom domain name `https://api.example.com` with the following API mappings:

1. `(none)` mapped to API 1\.

1. `orders/v1/items` mapped to API 2\.

1. `orders/v2/items` mapped to API 3\.

1. `orders/v2/items/categories` mapped to API 4\.


| Request | Selected API | Explanation | 
| --- | --- | --- | 
|  `https://api.example.com/orders/v1/items`  |  `API 2`  |  The request exactly matches this API mapping\.  | 
|  `https://api.example.com/orders/v2/items`  |  `API 3`  |  The request exactly matches this API mapping\.  | 
|  `https://api.example.com/orders/v1/items/123`  |  `API 2`  |  API Gateway chooses the mapping that has the longest matching path\. The `123` at the end of the request doesn't affect the selection\.  | 
|  `https://api.example.com/orders/v2/items/categories/5`  |  `API 4`  |  API Gateway chooses the mapping that has the longest matching path\.  | 
|  `https://api.example.com/customers`  |  `API 1`  |  API Gateway uses the empty mapping as a catch\-all\.  | 

## Restrictions<a name="rest-api-mappings-restrictions"></a>
+ In an API mapping, the custom domain name and mapped APIs must be in the same AWS account\.
+ API mappings must contain only letters, numbers, and the following characters: `$-_.+!*'()/`\.
+ The maximum length for the path in an API mapping is 300 characters\.
+ You can only map HTTP APIs to a regional custom domain name with the TLS 1\.2 security policy\.
+ You can't map WebSocket APIs to the same custom domain name as an HTTP API or REST API\.

## Create an API mapping<a name="rest-api-mappings-examples"></a>

To create an API mapping, you must first create a custom domain name, API, and stage\. For information about creating a custom domain name, see [Setting up a regional custom domain name in API Gateway](apigateway-regional-api-custom-domain-create.md)\.

For example AWS Serverless Application Model templates that create all resources, see [Sessions With SAM](https://github.com/aws-samples/sessions-with-aws-sam/tree/master/custom-domains) on GitHub\.

------
#### [ AWS Management Console ]

**To create an API mapping**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose **Custom domain names**\.

1. Select a custom domain name that you've already created\.

1. Choose **API mappings**\.

1. Choose **Configure API mappings**\.

1. Choose **Add new mapping**\.

1. Enter an **API**, a **Stage**, and optionally a **Path**\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

The following AWS CLI command creates an API mapping\. In this example, API Gateway sends requests to `api.example.com/v1/orders` to the specified API and stage\.

**Note**  
To create API mappings with multiple levels, you must use `apigatewayv2`\.

```
aws apigatewayv2 create-api-mapping \
    --domain-name api.example.com \
    --api-mapping-key v1/orders \
    --api-id a1b2c3d4 \
    --stage test
```

------
#### [ AWS CloudFormation ]

The following AWS CloudFormation example creates an API mapping\.

**Note**  
To create API mappings with multiple levels, you must use `AWS::ApiGatewayV2`\.

```
MyApiMapping:
  Type: 'AWS::ApiGatewayV2::ApiMapping'
  Properties:
    DomainName: api.example.com.com
    ApiMappingKey: 'orders/v2/items'
    ApiId: !Ref MyApi
    Stage: !Ref MyStage
```

------