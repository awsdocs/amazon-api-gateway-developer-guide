# Setting up data transformations for REST APIs<a name="rest-api-data-transformations"></a>

 In API Gateway, an API's method request can take a payload in a different format from the corresponding integration request payload, as required in the backend\. Similarly, the backend may return an integration response payload different from the method response payload, as expected by the frontend\. API Gateway lets you use mapping templates to map the payload from a method request to the corresponding integration request and from an integration response to the corresponding method response\. 

A *mapping template* is a script expressed in [Velocity Template Language \(VTL\)](https://velocity.apache.org/engine/devel/vtl-reference.html) and applied to the payload using [JSONPath expressions](http://goessner.net/articles/JsonPath/)\. 

The payload can have a *data model* according to the [JSON schema draft 4](https://tools.ietf.org/html/draft-zyp-json-schema-04)\. You must define the model in order to have API Gateway to generate a SDK or to enable basic request validation for your API\. You don't have to define any model to create a mapping template\. However, a model can help you create a template because API Gateway will generate a template blueprint based on a provided model\. 

This section explains how to map the API request and response payload using models and mapping templates\.

**Topics**
+ [Working with models and mapping templates](models-mappings.md)
+ [Set up request and response data mappings using the API Gateway console](how-to-method-settings-execution-console.md)
+ [Models and mapping template examples](rest-api-develop-models-mapping-examples.md)
+ [Amazon API Gateway API request and response data mapping reference](request-response-data-mappings.md)
+ [API Gateway mapping template and access logging variable reference](api-gateway-mapping-template-reference.md)