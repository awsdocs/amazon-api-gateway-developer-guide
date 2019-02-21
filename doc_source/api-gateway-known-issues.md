# Amazon API Gateway Known Issues<a name="api-gateway-known-issues"></a>

**Topics**
+ [Amazon API Gateway Known Issues for REST and WebSocket APIs](#api-gateway-known-issues-all-apis)
+ [Amazon API Gateway Known Issues for WebSocket APIs](#api-gateway-known-issues-websocket-apis)
+ [Amazon API Gateway Known Issues for REST APIs](#api-gateway-known-issues-rest-apis)

## Amazon API Gateway Known Issues for REST and WebSocket APIs<a name="api-gateway-known-issues-all-apis"></a>
+ API Gateway does not support wildcard subdomain names \(of the `*.domain` form\)\. However, it does support wildcard certificates \(certificates for wildcard subdomain names\)\.
+ API Gateway does not support sharing a custom domain name across REST and WebSocket APIs\.
+ The `/ping` and `/sping` paths are reserved for the service health check\. Use of these for API root\-level resources with custom domains will fail to produce the expected result\.
+ API Gateway currently limits log events to 1024 bytes\. Log events larger than 1024 bytes, such as request and response bodies, will be truncated by API Gateway before submission to CloudWatch Logs\.
+ CloudWatch Metrics currently limits dimension names and values to 255 valid XML characters\. \(For more information, see the [CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html#Dimension)\.\) Dimension values are a function of user\-defined names, including API name, label \(stage\) name, and resource name\. When choosing these names, be careful not to exceed CloudWatch Metrics limits\.

## Amazon API Gateway Known Issues for WebSocket APIs<a name="api-gateway-known-issues-websocket-apis"></a>
+ API Gateway supports message payloads up to 128 KB with a maximum frame size of 32 KB\. If a message exceeds 32 KB, you must split it into multiple frames, each 32 KB or smaller\. If a larger message is received, the connection is closed with code 1009\.

## Amazon API Gateway Known Issues for REST APIs<a name="api-gateway-known-issues-rest-apis"></a>
+ The plain text pipe character \(`|`\) is not supported for any request URL query string and must be URL\-encoded\.
+ The semicolon character \(`;`\) is not supported for any request URL query string and results in the data being split\.
+ When using the API Gateway console to test an API, you may get an "unknown endpoint errors" response if a self\-signed certificate is presented to the backend, the intermediate certificate is missing from the certificate chain, or any other unrecognizable certificate\-related exceptions thrown by the backend\.
+ For an API [https://docs.aws.amazon.com/apigateway/api-reference/resource/resource/](https://docs.aws.amazon.com/apigateway/api-reference/resource/resource/) or [https://docs.aws.amazon.com/apigateway/api-reference/resource/method/](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/) entity with a private integration, you should delete it after removing any hard\-coded reference of a [https://docs.aws.amazon.com/apigateway/api-reference/resource/vpc-link/](https://docs.aws.amazon.com/apigateway/api-reference/resource/vpc-link/)\. Otherwise, you have a dangling integration and receive an error stating that the VPC link is still in use even when the `Resource` or `Method` entity is deleted\. This behavior does not apply when the private integration references `VpcLink` through a stage variable\.
+ The following backends may not support SSL client authentication in a way that's compatible with API Gateway:
  + [NGINX](https://nginx.org/en/)
  +  [Heroku](https://www.heroku.com/)
+ API Gateway supports most of the [OpenAPI 2\.0 specification](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) and the [OpenAPI 3\.0 specification](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.1.md), with the following exceptions:
  + API Gateway models are defined using [JSON schema draft 4](https://tools.ietf.org/html/draft-zyp-json-schema-04), instead of the JSON schema used by OpenAPI\.
  + The `additionalProperties` and `anyOf `fields are not supported in Models\.
  + The `discriminator` parameter is not supported in any schema object\.
  + The `example` tag is not supported\.
  + `exclusiveMinimum` is not supported by API Gateway
  + The `maxItems` and `minItems` tags are not included in simple request validation\. To work around this, update the model after import before doing validation\.
  + `oneOf` is not supported by API Gateway
  + `pattern` is not supported by API Gateway
  + The `readOnly` field is not supported\.
  + Response definitions of the `"500": {"$ref": "#/responses/UnexpectedError"}` form is not supported in the OpenAPI document root\. To work around this, replace the reference by the inline schema\.
  + Numbers of the `Int32` or `Int64` type is not supported\. An example is shown as follows:

    ```
    "elementId": {
        "description": "Working Element Id",
        "format": "int32",
        "type": "number"
    }
    ```
  + Decimal number format type \(`"format": "decimal"`\) is not supported in a schema definition\.
  + In method responses, schema definition must be of an object type and cannot be of primitive types\. For example, `"schema": { "type": "string"}` is not supported\. However, you can work around this using the following object type:

    ```
    "schema": {
         "$ref": "#/definitions/StringResponse"
                }
    
     "definitions": {
        "StringResponse": {
          "type": "string"
        }
      }
    ```
+ API Gateway enacts the following restrictions and limitations when handling methods with either Lambda integration or HTTP integration\.
  + Header names and query parameters are processed in a case\-sensitive way\.
  + The following headers may be dropped, remapped, or otherwise modified when sent to your integration endpoint or sent back by your integration endpoint:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-known-issues.html)
+ The Android SDK of an API generated by API Gateway uses the `java.net.HttpURLConnection` class\. This class will throw an unhandled exception, on devices running Android 4\.4 and earlier, for a 401 response resulted from remapping of the `WWW-Authenticate` header to `X-Amzn-Remapped-WWW-Authenticate`\. 
+  Unlike API Gateway\-generated Java, Android and iOS SDKs of an API, the JavaScript SDK of an API generated by API Gateway does not support retries for 500\-level errors\. 
+  The test invocation of a method uses the default content type of `application/json` and ignores specifications of any other content types\. 