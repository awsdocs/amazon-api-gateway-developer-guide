# Enable request validation in API Gateway<a name="api-gateway-method-request-validation"></a>

 You can configure API Gateway to perform basic validation of an API request before proceeding with the integration request\. When the validation fails, API Gateway immediately fails the request, returns a 400 error response to the caller, and publishes the validation results in CloudWatch Logs\. This reduces unnecessary calls to the backend\. More importantly, it lets you focus on the validation efforts specific to your application\. 

**Topics**
+ [Overview of basic request validation in API Gateway](#api-gateway-request-validation-basic-definitions)
+ [Set up basic request validation in API Gateway](api-gateway-request-validation-set-up.md)
+ [Test basic request validation in API Gateway](api-gateway-request-validation-test.md)
+ [OpenAPI definitions of a sample API with basic request validation](api-gateway-request-validation-sample-api-swagger.md)

## Overview of basic request validation in API Gateway<a name="api-gateway-request-validation-basic-definitions"></a>

 API Gateway can perform the basic validation\. This enables you, the API developer, to focus on app\-specific deep validation in the backend\. For the basic validation, API Gateway verifies either or both of the following conditions: 
+  The required request parameters in the URI, query string, and headers of an incoming request are included and non\-blank\. 
+  The applicable request payload adheres to the configured [JSON schema](https://tools.ietf.org/html/draft-zyp-json-schema-04) request [model](how-to-create-model.md) of the method\. 

 To enable basic validation, you specify validation rules in a [request validator](https://docs.aws.amazon.com/apigateway/api-reference/resource/request-validator/), add the validator to the API's [map of request validators](https://docs.aws.amazon.com/apigateway/api-reference/resource/request-validators/), and assign the validator to individual API methods\. 

**Note**  
 Request body validation and [request body passthrough](integration-passthrough-behaviors.md) are two separate issues\. When a request payload cannot be validated because no model schema can be matched, you can choose to passthrough or block the original payload\.   
For example, when you enable request validation with a mapping template for the `application/json` media type, you might want to pass an XML payload through to the backend even though the enabled request validation will fail\. This might be the case if you expect to support the XML payload on the method in the future\. To fail the request with an XML payload, you must explicitly choose the `NEVER` option for the content passthrough behavior\. 