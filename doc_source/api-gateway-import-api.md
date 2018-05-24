# Import an API into API Gateway<a name="api-gateway-import-api"></a>

 You can use the API Gateway Import API feature to import an API from an external definition file into API Gateway\. Currently, the Import API feature supports [Swagger v2\.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) definition files\. 

 With the Import API, you can either create a new API by submitting a [POST request](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) that includes a Swagger definition in the payload and endpoint configuration, or you can update an existing API by using a [PUT request](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-put/) that contains a Swagger definition in the payload\. You can update an API by overwriting it with a new definition, or merge a definition with an existing API\. You specify the options using a `mode` query parameter in the request URL \. 

**Note**  
 For RAML API definitions, you can continue to use [API Gateway Importer](https://github.com/awslabs/aws-apigateway-importer)\. 

 Besides making explicit calls to the REST API, as described below, you can also use the Import API feature in the API Gateway console\. For a quick start to using the Import API feature from the API Gateway console, see [Build an API Gateway API from an Example](api-gateway-create-api-from-example.md)\. 

**Topics**
+ [Import an Edge\-Optimized API into API Gateway](import-edge-optimized-api.md)
+ [Import a Regional API into API Gateway](import-export-api-endpoints.md)
+ [Import a Swagger File to Update an Existing API Definition](api-gateway-import-api-update.md)
+ [Set the Swagger `basePath` Property](api-gateway-import-api-basePath.md)
+ [Errors and Warnings during Import](api-gateway-import-api-errors-warnings.md)