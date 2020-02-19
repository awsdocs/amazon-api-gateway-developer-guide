# Import a REST API into API Gateway<a name="api-gateway-import-api"></a>

 You can use the API Gateway Import API feature to import a REST API from an external definition file into API Gateway\. Currently, the Import API feature supports [OpenAPI v2\.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) and [OpenAPI v3\.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.1.md) definition files\. You can update an API by overwriting it with a new definition, or merge a definition with an existing API\. You specify the options using a `mode` query parameter in the request URL\. 

For a tutorial on using the Import API feature from the API Gateway console, see [TUTORIAL: Create a REST API by Importing an Example](api-gateway-create-api-from-example.md)\.

**Topics**
+ [Import an Edge\-Optimized API into API Gateway](import-edge-optimized-api.md)
+ [Import a Regional API into API Gateway](import-export-api-endpoints.md)
+ [Import an OpenAPI File to Update an Existing API Definition](api-gateway-import-api-update.md)
+ [Set the OpenAPI `basePath` Property](api-gateway-import-api-basePath.md)
+ [AWS Variables for OpenAPI Import](import-api-aws-variables.md)
+ [Errors and Warnings during Import](api-gateway-import-api-errors-warnings.md)