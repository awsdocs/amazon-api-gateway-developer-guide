# Amazon API Gateway API Request and Response Data Mapping Reference<a name="request-response-data-mappings"></a>

 This section explains how to set up data mappings from an API's method request data, including other data stored in [`context`](api-gateway-mapping-template-reference.md#context-variable-reference), [`stage`](api-gateway-mapping-template-reference.md#stagevariables-template-reference), or [`util`](api-gateway-mapping-template-reference.md#util-template-reference) variables, to the corresponding integration request parameters and from an integration response data, including the other data, to the method response parameters\. The method request data includes request parameters \(path, query string, headers\) and the body The integration response data includes response parameters \(headers\) and the body\. For more information about using the stage variables, see [Amazon API Gateway Stage Variables Reference](aws-api-gateway-stage-variables-reference.md)\. 

**Topics**
+ [Map Method Request Data to Integration Request Parameters](#mapping-request-parameters)
+ [Map Integration Response Data to Method Response Headers](#mapping-response-parameters)
+ [Map Request and Response Payloads between Method and Integration](#transforming-request-response-body)
+ [Integration Passthrough Behaviors](integration-passthrough-behaviors.md)

## Map Method Request Data to Integration Request Parameters<a name="mapping-request-parameters"></a>

 Integration request parameters, in the form of path variables, query strings or headers, can be mapped from any defined method request parameters and the payload\. 


**Integration request data mapping expressions**  

| Mapped data source | Mapping expression | 
| --- | --- | 
| Method request path | method\.request\.path\.PARAM\_NAME | 
| Method request query string | method\.request\.querystring\.PARAM\_NAME | 
| Method request header | method\.request\.header\.PARAM\_NAME | 
| Method request body | method\.request\.body | 
| Method request body \(JsonPath\) | method\.request\.body\.JSONPath\_EXPRESSION\. | 
| Stage variables | stageVariables\.VARIABLE\_NAME | 
| Context variables | context\.VARIABLE\_NAME that must be one of the [supported context variables](api-gateway-mapping-template-reference.md#context-variable-reference)\. | 
| Static value | 'STATIC\_VALUE'\. The STATIC\_VALUE is a string literal and must be enclosed within a pair of single quotes\. | 

 Here, *PARAM\_NAME* is the name of a method request parameter of the given parameter type\. It must have been defined before it can be referenced\. *JSONPath\_EXPRESSION* is a JSONPath expression for a JSON field of the body of a request or response\. However, the "$\." prefix is omitted in this syntax\. 

**Example mappings from method request parameter in Swagger**  
 The following example shows a Swagger snippet that maps 1\) the method request's header, named `methodRequestHeadParam`, into the integration request path parameter, named `integrationPathParam`; 2\) the method request query string, named `methodRequestQueryParam`, into the integration request query string, named `integrationQueryParam`\.   

```
...
"requestParameters" : {
            
    "integration.request.path.integrationPathParam" : "method.request.header.methodRequestHeaderParam",        
    "integration.request.querystring.integrationQueryParam" : "method.request.querystring.methodRequestQueryParam"
            
}
...
```

 Integration request parameters can also be mapped from fields in the JSON request body using a [JSONPath expression](http://goessner.net/articles/JsonPath/index.html#e2)\. The following table shows the mapping expressions for a method request body and its JSON fields\. 

**Example mapping from method request body in Swagger**  
 The following example shows a Swagger snippet that maps 1\) the method request body to the integration request header, named `body-header`, and 2\) a JSON field of the body, as expressed by a JSON expression \(`petstore.pets[0].name`, without the `$.` prefix\)\.   

```
...
"requestParameters" : {
            
    "integration.request.header.body-header" : "method.request.body",
    "integration.request.path.pet-name" : "method.request.body.petstore.pets[0].name",
            
}
...
```

## Map Integration Response Data to Method Response Headers<a name="mapping-response-parameters"></a>

 Method response header parameters can be mapped from any integration response header or integration response body, `$context` variables, or static values\.  


**Method response header mapping expressions**  

| Mapped Data Source | Mapping expression | 
| --- | --- | 
| Integration response header | integration\.response\.header\.PARAM\_NAME | 
| Integration response body | integration\.response\.body | 
| Integration response body \(JsonPath\) | integration\.response\.body\.JSONPath\_EXPRESSION | 
| Stage variable | stageVariables\.VARIABLE\_NAME | 
| Context variable | context\.VARIABLE\_NAME that must be one of the [supported context variables](api-gateway-mapping-template-reference.md#context-variable-reference)\. | 
| Static value | 'STATIC\_VALUE'\. The STATIC\_VALUE is a string literal and must be enclosed within a pair of single quotes\. | 

**Example data mapping from integration response in Swagger**  
 The following example shows a Swagger snippet that maps 1\) the integration response's `redirect.url`, JSONPath field into the request response's `location` header; and 2\) the integration response's `x-app-id` header to the method response's `id` header\.   

```
...
"responseParameters" : {
            
    "method.response.header.location" : "integration.response.body.redirect.url",
    "method.response.header.id" : "integration.response.header.x-app-id",
            
}
...
```

## Map Request and Response Payloads between Method and Integration<a name="transforming-request-response-body"></a>

 API Gateway uses [Velocity Template Language \(VTL\)](http://velocity.apache.org/engine/devel/vtl-reference-guide.html) engine to process body [mapping templates](models-mappings.md#models-mappings-mappings) for the integration request and integration response\. The mapping templates translate method request payloads to the corresponding integration request payloads and translate integration response bodies to the method response bodies\. 

 The VTL templates use JSONPath expressions, other parameters such as calling contexts and stage variables, and utility functions to process the JSON data\. 

 If a model is defined to describe the data structure of a payload, API Gateway can use the model to generate a skeletal mapping template for an integration request or integration response\. You can use the skeletal template as an aid to customize and expand the mapping VTL script\. However, you can create a mapping template from scratch without defining a model for the payload's data structure\. 

### Select a VTL Mapping Template<a name="selecting-mapping-templates"></a>

 API Gateway uses the following logic to select a mapping template, in [Velocity Template Language \(VTL\)](http://velocity.apache.org/engine/devel/vtl-reference-guide.html), to map the payload from a method request to the corresponding integration request or to map the payload from an integration response to the corresponding method response\. 

 For a request payload, API Gateway uses the request’s `Content-Type` header value as the key to select the mapping template for the request payload\. For a response payload, API Gateway uses the incoming request’s `Accept` header value as the key to select the mapping template\. 

 When the `Content-Type` header is absent in the request, API Gateway assumes that its default value is `application/json`\. For such a request, API Gateway uses `application/json` as the default key to select the mapping template, if one is defined\. When no template matches this key, API Gateway passes the payload through unmapped if the [passthroughBehavior](http://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#passthroughBehavior) property is set to `WHEN_NO_MATCH` or `WHEN_NO_TEMPLATES`\. 

 When the `Accept` header is not specified in the request, API Gateway assumes that its default value is `application/json`\. In this case, API Gateway selects an existing mapping template for `application/json` to map the response payload\. If no template is defined for `application/json`, API Gateway selects the first existing template and uses it as the default to map the response payload\. Similarly, API Gateway uses the first existing template when the specified `Accept` header value does not match any existing template key\. If no template is defined, API Gateway simply passes the response payload through unmapped\. 

 For example, suppose that an API has a `application/json` template defined for a request payload and has a `application/xml` template defined for the response payload\. If the client sets the `"Content-Type : application/json"`, and `"Accept : application/xml"` headers in the request, both the request and response payloads will be processed with the corresponding mapping templates\. If the `Accept:application/xml` header is absent, the `application/xml` mapping template will be used to map the response payload\. To return the response payload unmapped instead, you must set up an empty template for `application/json`\. 

 Only the MIME type is used from the `Accept` and `Content-Type` headers when selecting a mapping template\. For example, a header of `"Content-Type: application/json; charset=UTF-8"` will have a request template with the `application/json` key selected\. 