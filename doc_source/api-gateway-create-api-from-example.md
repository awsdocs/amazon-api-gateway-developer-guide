# Build an API Gateway API from an Example<a name="api-gateway-create-api-from-example"></a>

 To help you get started with basic work flow to build and test an API Gateway API, you can use the Amazon API Gateway console to create and test a simple API with the HTTP integration for a PetStore website\. The API definition is preconfigured as a Swagger 2\.0 file\. After loading the API definition into API Gateway, you can use the API Gateway console to examine the API's basic structure or simply deploy and test the API\. 

 The example API supports the following methods for a client to access the HTTP backend website of `http://petstore-demo-endpoint.execute-api.com/petstore/pets`\. 
+ `GET /`: for read access of the API's root resource that is not integrated with any backend endpoint\. API Gateway responds with an overview of the PetStore website\. This is an example of the `MOCK` integration type\.
+ `GET /pets`: for read access to the API's `/pets` resource that is integrated with the like\-named backend `/pets` resource\. The backend returns a page of available pets in the PetStore\. This is an example of the `HTTP` integration type\. The URL of the integration endpoint is `http://petstore-demo-endpoint.execute-api.com/petstore/pets`\.
+ `POST /pets`: for write access to the API's `/pets` resource that is integrated with the backend `/petstore/pets` resource\. Upon receiving a correct request, the backend adds the specified pet to the PetStore and return the result to the caller\. The integration is also `HTTP`\.
+ `GET /pets/{petId}`: for read access to a pet as identified by a `petId` value as specified as a path variable of the incoming request URL\. This method also has the `HTTP` integration type\. The backend returns the specified pet found in the PetStore\. The URL of the backend HTTP endpoint is `http://petstore-demo-endpoint.execute-api.com/petstore/pets/n`, where `n` is an integer as the identifier of the queried pet\.

 The API supports CORS access via the `OPTIONS` methods of the `MOCK` integration type\. API Gateway returns the required headers supporting CORS access\. 

**Topics**
+ [Create and Test an API from the Example in the API Gateway Console](api-gateway-create-api-from-example-console.md)
+ [Next Step](api-gateway-create-api-from-example-next.md)
+ [See Also](#api-gateway-create-api-from-example-see-also)

## See Also<a name="api-gateway-create-api-from-example-see-also"></a>

[Use API Gateway Lambda Authorizers](apigateway-use-lambda-authorizer.md), [Deploying an API in Amazon API Gateway](how-to-deploy-api.md)