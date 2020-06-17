# Working with binary media types for REST APIs<a name="api-gateway-payload-encodings"></a>

 In API Gateway, the API request and response can have a text or binary payload\. A text payload is a `UTF-8`\-encoded JSON string, and a binary payload is anything other than a text payload\. The binary payload can be, for example, a JPEG file, a GZip file, or an XML file\. 

 By default, API Gateway treats the message body as a text payload and applies any preconfigured mapping template to transform the JSON string\. If no mapping template is specified, API Gateway can pass the text payload through to or from the integration endpoint without modification, provided that the passthrough behavior is enabled on the API method\. For a binary payload, API Gateway simply passes through the message as\-is\. 

 For API Gateway to pass binary payloads, you add the media types to the [binaryMediaTypes](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/#binaryMediaTypes) list of the `RestApi` resource or set the [contentHandling](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#contentHandling) properties on the [Integration](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) and the [IntegrationResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/) resources\. The `contentHandling` value can be `CONVERT_TO_BINARY`, `CONVERT_TO_TEXT`, or undefined\. 

Depending on the `contentHandling` value, and whether the `Content-Type` header of the response or the `Accept` header of the incoming request matches an entry in the `binaryMediaTypes` list, API Gateway can encode the raw binary bytes as a base64\-encoded string, decode a base64\-encoded string back to its raw bytes, or pass the body through without modification\. 

You must configure the API as follows to support binary payloads for your API in API Gateway: 
+ Add the desired binary media types to the `binaryMediaTypes` list on the [RestApi](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/) resource\. If this property and the `contentHandling` property are not defined, the payloads are handled as UTF\-8 encoded JSON strings\.
+ Set the `contentHandling` property of the [Integration](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) resource to `CONVERT_TO_BINARY` to have the request payload converted from a base64\-encoded string to its binary blob, or set the property to `CONVERT_TO_TEXT` to have the request payload converted from a binary blob to a base64\-encoded string\. If this property is not defined, API Gateway passes the payload through without modification\. This occurs when the `Content-Type` header value matches one of the `binaryMediaTypes` entries and the [passthrough behaviors](integration-passthrough-behaviors.md) are also enabled for the API\. 
+ Set the `contentHandling` property of the [IntegrationResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/) resource to `CONVERT_TO_BINARY` to have the response payload converted from a base64\-encoded string to its binary blob, or set the property to `CONVERT_TO_TEXT` to have the response payload converted from a binary blob to a base64\-encoded string\. 

  If `contentHandling` isn't defined, and if the `Content-Type` header of the response and the `Accept` header of the original request match an entry of the `binaryMediaTypes` list, API Gateway passes through the body\. This occurs when the `Content-Type` header and the `Accept` header are the same\. Otherwise, API Gateway converts the response body to the type specified in the `Accept` header\. 

**Tip**  
When a request contains multiple media types in its `Accept` header, API Gateway only honors the first `Accept` media type\. In the situation where you can't control the order of the `Accept` media types and the media type of your binary content isn't the first in the list, you can add the first `Accept` media type in the `binaryMediaTypes` list of your API\. API Gateway returns your content as binary\.   
For example, to send a JPEG file using an `<img>` element in a browser, the browser might send `Accept:image/webp,image/*,*/*;q=0.8` in a request\. By adding `image/webp` to the `binaryMediaTypes` list, the endpoint receives the JPEG file as binary\. 

**Topics**
+ [Content type conversions in API Gateway](api-gateway-payload-encodings-workflow.md)
+ [Enabling binary support using the API Gateway console](api-gateway-payload-encodings-configure-with-console.md)
+ [Enabling binary support using the API Gateway REST API](api-gateway-payload-encodings-configure-with-control-service-api.md)
+ [Import and export content encodings](api-gateway-payload-encodings-import-and-export.md)
+ [Examples of binary support](api-gateway-content-encodings-examples.md)