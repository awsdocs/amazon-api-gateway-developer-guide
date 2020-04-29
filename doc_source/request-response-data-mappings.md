# Amazon API Gateway API request and response data mapping reference<a name="request-response-data-mappings"></a>

 This section explains how to set up data mappings from an API's method request data, including other data stored in [`context`](api-gateway-mapping-template-reference.md#context-variable-reference), [`stage`](api-gateway-mapping-template-reference.md#stagevariables-template-reference), or [`util`](api-gateway-mapping-template-reference.md#util-template-reference) variables, to the corresponding integration request parameters and from an integration response data, including the other data, to the method response parameters\. The method request data includes request parameters \(path, query string, headers\) and the body\. The integration response data includes response parameters \(headers\) and the body\. For more information about using the stage variables, see [Amazon API Gateway stage variables reference](aws-api-gateway-stage-variables-reference.md)\. 

**Topics**
+ [Map method request data to integration request parameters](#mapping-request-parameters)
+ [Map integration response data to method response headers](#mapping-response-parameters)
+ [Map request and response payloads between method and integration](#transforming-request-response-body)
+ [Integration passthrough behaviors](integration-passthrough-behaviors.md)

## Map method request data to integration request parameters<a name="mapping-request-parameters"></a>

Integration request parameters, in the form of path variables, query strings or headers, can be mapped from any defined method request parameters and the payload\.

In the following table, *`PARAM_NAME`* is the name of a method request parameter of the given parameter type\. It must match the regular expression `'^[a-zA-Z0-9._$-]+$]'`\. It must have been defined before it can be referenced\. *`JSONPath_EXPRESSION`* is a JSONPath expression for a JSON field of the body of a request or response\.

**Note**  
The `"$"` prefix is omitted in this syntax\.


**Integration request data mapping expressions**  

| Mapped data source | Mapping expression | 
| --- | --- | 
| Method request path | method\.request\.path\.PARAM\_NAME | 
| Method request query string | method\.request\.querystring\.PARAM\_NAME | 
| Multi\-value method request query string | method\.request\.multivaluequerystring\.PARAM\_NAME | 
| Method request header | method\.request\.header\.PARAM\_NAME | 
| Multi\-value method request header | method\.request\.multivalueheader\.PARAM\_NAME | 
| Method request body | method\.request\.body | 
| Method request body \(JsonPath\) | method\.request\.body\.JSONPath\_EXPRESSION\. | 
| Stage variables | stageVariables\.VARIABLE\_NAME | 
| Context variables | context\.VARIABLE\_NAME that must be one of the [supported context variables](api-gateway-mapping-template-reference.md#context-variable-reference)\. | 
| Static value | 'STATIC\_VALUE'\. The STATIC\_VALUE is a string literal and must be enclosed within a pair of single quotes\. | 

**Example Mappings from method request parameter in OpenAPI**  
The following example shows an OpenAPI snippet that maps:  
+ the method request's header, named `methodRequestHeaderParam`, into the integration request path parameter, named `integrationPathParam`
+ the multi\-value method request query string, named `methodRequestQueryParam`, into the integration request query string, named `integrationQueryParam`

```
...
"requestParameters" : {
            
    "integration.request.path.integrationPathParam" : "method.request.header.methodRequestHeaderParam",        
    "integration.request.querystring.integrationQueryParam" : "method.request.multivaluequerystring.methodRequestQueryParam"
            
}
...
```

 Integration request parameters can also be mapped from fields in the JSON request body using a [JSONPath expression](http://goessner.net/articles/JsonPath/index.html#e2)\. The following table shows the mapping expressions for a method request body and its JSON fields\. 

**Example Mapping from method request body in OpenAPI**  
 The following example shows an OpenAPI snippet that maps 1\) the method request body to the integration request header, named `body-header`, and 2\) a JSON field of the body, as expressed by a JSON expression \(`petstore.pets[0].name`, without the `$.` prefix\)\.   

```
...
"requestParameters" : {
            
    "integration.request.header.body-header" : "method.request.body",
    "integration.request.path.pet-name" : "method.request.body.petstore.pets[0].name",
            
}
...
```

## Map integration response data to method response headers<a name="mapping-response-parameters"></a>

 Method response header parameters can be mapped from any integration response header or integration response body, `$context` variables, or static values\.  


**Method response header mapping expressions**  

| Mapped data source | Mapping expression | 
| --- | --- | 
| Integration response header | integration\.response\.header\.PARAM\_NAME | 
| Integration response header | integration\.response\.multivalueheader\.PARAM\_NAME | 
| Integration response body | integration\.response\.body | 
| Integration response body \(JsonPath\) | integration\.response\.body\.JSONPath\_EXPRESSION | 
| Stage variable | stageVariables\.VARIABLE\_NAME | 
| Context variable | context\.VARIABLE\_NAME that must be one of the [supported context variables](api-gateway-mapping-template-reference.md#context-variable-reference)\. | 
| Static value | 'STATIC\_VALUE'\. The STATIC\_VALUE is a string literal and must be enclosed within a pair of single quotes\. | 

**Example Data mapping from integration response in OpenAPI**  
 The following example shows an OpenAPI snippet that maps 1\) the integration response's `redirect.url`, JSONPath field into the request response's `location` header; and 2\) the integration response's `x-app-id` header to the method response's `id` header\.   

```
...
"responseParameters" : {
            
    "method.response.header.location" : "integration.response.body.redirect.url",
    "method.response.header.id" : "integration.response.header.x-app-id",
    "method.response.header.items" : "integration.response.multivalueheader.item",
            
}
...
```

## Map request and response payloads between method and integration<a name="transforming-request-response-body"></a>

 API Gateway uses [Velocity Template Language \(VTL\)](https://velocity.apache.org/engine/devel/vtl-reference.html) engine to process body [mapping templates](rest-api-data-transformations.md#models-mappings-mappings) for the integration request and integration response\. The mapping templates translate method request payloads to the corresponding integration request payloads and translate integration response bodies to the method response bodies\. 

 The VTL templates use JSONPath expressions, other parameters such as calling contexts and stage variables, and utility functions to process the JSON data\. 

 If a model is defined to describe the data structure of a payload, API Gateway can use the model to generate a skeletal mapping template for an integration request or integration response\. You can use the skeletal template as an aid to customize and expand the mapping VTL script\. However, you can create a mapping template from scratch without defining a model for the payload's data structure\. 

### Select a VTL mapping template<a name="selecting-mapping-templates"></a>

 API Gateway uses the following logic to select a mapping template, in [Velocity Template Language \(VTL\)](https://velocity.apache.org/engine/devel/vtl-reference.html), to map the payload from a method request to the corresponding integration request or to map the payload from an integration response to the corresponding method response\. 

 For a request payload, API Gateway uses the request’s `Content-Type` header value as the key to select the mapping template for the request payload\. For a response payload, API Gateway uses the incoming request’s `Accept` header value as the key to select the mapping template\. 

 When the `Content-Type` header is absent in the request, API Gateway assumes that its default value is `application/json`\. For such a request, API Gateway uses `application/json` as the default key to select the mapping template, if one is defined\. When no template matches this key, API Gateway passes the payload through unmapped if the [passthroughBehavior](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#passthroughBehavior) property is set to `WHEN_NO_MATCH` or `WHEN_NO_TEMPLATES`\. 

 When the `Accept` header is not specified in the request, API Gateway assumes that its default value is `application/json`\. In this case, API Gateway selects an existing mapping template for `application/json` to map the response payload\. If no template is defined for `application/json`, API Gateway selects the first existing template and uses it as the default to map the response payload\. Similarly, API Gateway uses the first existing template when the specified `Accept` header value does not match any existing template key\. If no template is defined, API Gateway simply passes the response payload through unmapped\. 

 For example, suppose that an API has a `application/json` template defined for a request payload and has a `application/xml` template defined for the response payload\. If the client sets the `"Content-Type : application/json"`, and `"Accept : application/xml"` headers in the request, both the request and response payloads will be processed with the corresponding mapping templates\. If the `Accept:application/xml` header is absent, the `application/xml` mapping template will be used to map the response payload\. To return the response payload unmapped instead, you must set up an empty template for `application/json`\. 

 Only the MIME type is used from the `Accept` and `Content-Type` headers when selecting a mapping template\. For example, a header of `"Content-Type: application/json; charset=UTF-8"` will have a request template with the `application/json` key selected\. 