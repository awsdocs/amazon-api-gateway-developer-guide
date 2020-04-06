# x\-amazon\-apigateway\-integration\.responses object<a name="api-gateway-swagger-extensions-integration-responses"></a>

 Defines the method's responses and specifies parameter mappings or payload mappings from integration responses to method responses\. 


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| Response status pattern | [x\-amazon\-apigateway\-integration\.response object](api-gateway-swagger-extensions-integration-response.md) |   Selection regular expression used to match the integration response to the method response\. For HTTP integrations, this regex applies to the integration response status code\. For Lambda invocations, the regex applies to the `errorMessage` field of the error information object returned by AWS Lambda as a failure response body when the Lambda function execution throws an [exception](url-lam-dev;nodejs-prog-mode-exceptions.html)\.  The *Response status pattern* property name refers to a response status code or regular expression describing a group of response status codes\. It does not correspond to any identifier of an [IntegrationResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/) resource in the API Gateway REST API\.  | 

## `x-amazon-apigateway-integration.responses` example<a name="api-gateway-swagger-extensions-responses-example"></a>

The following example shows a list of responses from `2xx` and `302` responses\. For the `2xx` response, the method response is mapped from the integration response's payload of the `application/json` or `application/xml` MIME type\. This response uses the supplied mapping templates\. For the `302` response, the method response returns a `Location` header whose value is derived from the `redirect.url` property on the integration response's payload\. 

```
"responses" : {
    "2\\d{2}" : {
        "statusCode" : "200",
        "responseTemplates" : {
            "application/json" : "#set ($root=$input.path('$')) { \"stage\": \"$root.name\", \"user-id\": \"$root.key\" }",
            "application/xml" : "#set ($root=$input.path('$')) <stage>$root.name</stage> "
        }
    },
    "302" : {
        "statusCode" : "302",
        "responseParameters" : {
            "method.response.header.Location": "integration.response.body.redirect.url"
        }
    }
}
```