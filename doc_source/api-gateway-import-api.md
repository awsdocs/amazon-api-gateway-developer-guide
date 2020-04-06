# Configuring a REST API using OpenAPI<a name="api-gateway-import-api"></a>

You can use API Gateway to import a REST API from an external definition file into API Gateway\. Currently, API Gateway supports [OpenAPI v2\.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) and [OpenAPI v3\.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.1.md) definition files\. You can update an API by overwriting it with a new definition, or you can merge a definition with an existing API\. You specify the options by using a `mode` query parameter in the request URL\. 

For a tutorial on using the Import API feature from the API Gateway console, see [Tutorial: Create a REST API by importing an example](api-gateway-create-api-from-example.md)\.

**Topics**
+ [Import an edge\-optimized API into API Gateway](import-edge-optimized-api.md)
+ [Import a regional API into API Gateway](import-export-api-endpoints.md)
+ [Import an OpenAPI file to update an existing API definition](api-gateway-import-api-update.md)
+ [Set the OpenAPI `basePath` property](api-gateway-import-api-basePath.md)
+ [AWS variables for OpenAPI import](import-api-aws-variables.md)
+ [Errors and warnings during import](api-gateway-import-api-errors-warnings.md)
+ [Export a REST API from API Gateway](api-gateway-export-api.md)