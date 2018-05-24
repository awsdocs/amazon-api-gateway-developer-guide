# Set up an Edge\-Optimized API Using the API Gateway REST API<a name="create-api-using-restapi"></a>

 Setting up an API using the API Gateway REST API involves working with API Gateway resources of the `[RestApi](http://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/)`, `[Resource](http://docs.aws.amazon.com/apigateway/api-reference/resource/resource/)`, `[Method](http://docs.aws.amazon.com/apigateway/api-reference/resource/method/)`, `[MethodResponse](http://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/)`, `[Integration](http://docs.aws.amazon.com/apigateway/api-reference/resource/integration/)`, and `[IntegrationResponse](http://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/)`types\. The following procedure walks through the basic steps to work with these API Gateway resources to set up the simple PetStore API\. 

**To create the simple PetStore API**

1.  To set up an edge\-optimized API, invoke the API Gateway's `[restapi:create](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-create/)` link\-relation to add an API Gateway resource of `[RestApi](http://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/)` to your account in a chosen region: 

   ```
   POST /restapis HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170511T214723Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170511/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=d0abd98a2a06199531c2916b162ede9f63a247032cdc8e4d077216446d13103c
   Cache-Control: no-cache
   Postman-Token: 0889d2b5-e507-6aab-f222-ab9548dbacaa
   
   {
       "name": "Simple PetStore (REST API)",
       "description": "A sample API Gateway API created using the REST API."
   }
   ```

    The successful request returns a response of the `201 Created` status code with a payload similar to the following output: 

   ```
   {
     "createdDate": "2017-05-11T21:47:24Z",
     "description": "A sample API Gateway API created using the REST API.",
       "endpointConfiguration": {
           "types": "EDGE"
       },
     "id": "x7hyqq0ik7",
     "name": "Simple PetStore (REST API)"
   }
   ```

    Note of the `id` value of the newly created `RestApi`\. You will use this `id` value in subsequent operations on this API\. A newly created `RestApi` comes with the API's root resource \(`/`\) of the API\. You need to specify the `id` value of this root resource to append a child resource, and to add a method on the root resource\. To get this API identifier, get the API's `resources` collection and then parse the result to obtain the `id` property value of the entry with the `path` value of `/`\. 

1.  To get the API root resource identifier, invoke the [restapi:resources](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-resources/) link\-relation: 

   ```
   GET /restapis/x7hyqq0ik7/resources HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170511T215738Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170511/us-west-2/apigateway/aws4_request, SignedHeaders=content-type;host;x-amz-date, Signature=76c24ef91d835b85313142bf75545c4ac4c212067e8188ee6a127c21dae09e29
   ```

   The request returns a response of the `200 OK` status code with a payload similar to the following output: 

   ```
   {
     "_embedded": {
       "item": {
         "_links": {
           "self": {
             "href": "/restapis/x7hyqq0ik7/resources/0f72nvvnkd"
           },
           "method:by-http-method": {
             "href": "/restapis/x7hyqq0ik7/resources/0f72nvvnkd/methods/{http_method}",
             "templated": true
           },
           "method:put": {
             "href": "/restapis/x7hyqq0ik7/resources/0f72nvvnkd/methods/{http_method}",
             "templated": true
           },
           "resource:create-child": {
             "href": "/restapis/x7hyqq0ik7/resources/0f72nvvnkd"
           },
           "resource:update": {
             "href": "/restapis/x7hyqq0ik7/resources/0f72nvvnkd"
           }
         },
         "id": "0f72nvvnkd",
         "path": "/"
       }
     }
   }
   ```

   The root resource identifier is the `id` value associated with the `path` value of `"/"`\. In this example, it is `0f72nvvnkd`\.

1.  To add a `pets` resource under the root resource \(`0f72nvvnkd`\) to represent the pets collection of the pet store, call the `[resource:create](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-create/)` link\-relation of API Gateway\. 

   ```
   POST /restapis/x7hyqq0ik7/resources/0f72nvvnkd HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T000729Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=8a7093411c97b0aa90f4b1890475d93cf20aa3732089da61e6deb410fbc6037d
   Cache-Control: no-cache
   Postman-Token: 48abcd2f-c357-9e44-669e-d8d813f876ca
   
   {
       "pathPart": "pets"
   }
   ```

   The successful response contains the newly created child resource \(`47rxl6`, `pets` or `/pets`\) and its parent \(`0f72nvvnkd`\)\.

   ```
   {
     ...,
     "id": "47rxl6",
     "parentId": "0f72nvvnkd",
     "path": "/pets",
     "pathPart": "pets"
   }
   ```

   Similarly, to add an individual pet under the `pets` collection \(as referenced the resource Id of `47rxl6`, invoke the following `resource:create` link\-relation:

   ```
   POST /restapis/x7hyqq0ik7/resources/47rxl6 HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T000729Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=8a7093411c97b0aa90f4b1890475d93cf20aa3732089da61e6deb410fbc6037d
   Cache-Control: no-cache
   Postman-Token: 48abcd2f-c357-9e44-669e-d8d813f876ca
   
   {
       "pathPart": "{petId}"
   }
   ```

   The resulting response looks like this one:

   ```
   {
     ...,
     "id": "ab34fgd",
     "parentId": "47rxl6",
     "path": "/pets/{petId}",
     "pathPart": "{petsId}"
   }
   ```

1.  To add a `GET` method to the API's `/pets` resource \(`47rxl6`\), invoke the following `[method:put](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-put/)` link\-relation of API Gateway: 

   ```
   PUT /restapis/x7hyqq0ik7/resources/47rxl6/methods/GET HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T000729Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=8a7093411c97b0aa90f4b1890475d93cf20aa3732089da61e6deb410fbc6037d
   Cache-Control: no-cache
   Postman-Token: 48abcd2f-c357-9e44-669e-d8d813f876ca
   
   {
       "authorizationType": "NONE"
   }
   ```

    The method is for open access because `authorization-type` is set to `NONE`\. To permit only authenticated users to call the method, you can use IAM roles and policies, a Lambda authorizer \(formerly known as a custom authorizer\), or an Amazon Cognito user pool\. For more information, see [ Controlling Access to an API in API Gateway](apigateway-control-access-to-api.md)\. 

    The successful request returns a `201 Created` response with a payload similar to the following: 

   ```
   {
     ...,
     "apiKeyRequired": false,
     "authorizationType": "NONE",
     "httpMethod": "GET"
   }
   ```

    To add a `GET` method to the API's `/pets/{petId}` resource \(`ab34fgd`\), invoke the following `[method:put](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-put/)` link\-relation of API Gateway: 

   ```
   PUT /restapis/x7hyqq0ik7/resources/ab34fgd/methods/GET HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T000729Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=8a7093411c97b0aa90f4b1890475d93cf20aa3732089da61e6deb410fbc6037d
   Cache-Control: no-cache
   Postman-Token: 48abcd2f-c357-9e44-669e-d8d813f876ca
   
   {
       "authorizationType": "NONE",
       "requestParameters": {
         "method.request.path.petId": true
       }
   }
   ```

   You must declare the method request path parameter of `petId` for API Gateway to map its dynamically set value to the corresponding integration request parameter before passing it to the backend\. You must always set a path parameter as required\. In addition, depending on API requirements, you can set up header and query parameters on a method request\. For `POST`, `PUT`, `PATCH`, or any other method taking a payload, you can define a model for the payload in the method request\. For more information about these settings, see [Set up API Methods in API Gateway ](how-to-method-settings.md)\. 

   The successful response has a status code of `201 Created` and a payload similar to the following:

   ```
   {
       "apiKeyRequired": false,
       "authorizationType": "NONE",
       "httpMethod": "GET",
       "requestParameters": {
           "method.request.path.petId": true
       }
   }
   ```

1. To add a method response of the `200` status code for the `GET /pets` method of the API, invoke the [methodresponse:put](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/methodresponse-put/) link\-relation, specifying `47rxl6` to reference the resource exposing this method:

   ```
   PUT /restapis/x7hyqq0ik7/resources/47rxl6/methods/GET/responses/200 HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T003943Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=229ef4cfba4bbe41132f36c027f0ae4449bb741671875075a4b216e9b778233e
   Cache-Control: no-cache
   Postman-Token: 268fcf18-92e4-dfea-821a-ebf4e1d0edfd
   
   {}
   ```

   The successful request returns a response of the `201 Created` status code with a payload similar to the following output:

   ```
   {
     ...,
     "statusCode": "200"
   }
   ```

   Similarly, to add a `200` response to the `GET /pets/{petId}` method, invoke the following `methodresponse:put` link\-relation, referencing the desired `/pets/{petId}` resource by its ID \(`ab34fgd`\):

   ```
   PUT /restapis/x7hyqq0ik7/resources/ab34fgd/methods/GET/responses/200 HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T003943Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=229ef4cfba4bbe41132f36c027f0ae4449bb741671875075a4b216e9b778233e
   Cache-Control: no-cache
   Postman-Token: 268fcf18-92e4-dfea-821a-ebf4e1d0edfd
   
   {}
   ```

    The API Gateway resources of `Method` and `MethodRepsonse` that you just set up define the client\-facing interface of the API\. For non\-proxy integrations, you must add and configure an API Gateway resource of `Integration` and `IntegrationResponse` to encapsulate the integration request submitted to the backend and the integration response returned by the backend\. For proxy integrations, however, you do not set up `Integration` and `IntegrationResponse`\.

1. To set up `Integration` for the `GET /pets` method, invoke the [integration:put](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/integration-put/) link\-relation of API Gateway, referencing the `/pets` resource by its ID value \(`47rxl6`\):

   ```
   PUT /restapis/x7hyqq0ik7/resources/47rxl6/methods/GET/integration HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T002249Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=10359971a8c54862a47e39d6a6e4b6e62c263e9a2b785b47b40c426c0aa61c19
   
   {
     "type" : "HTTP",
     "httpMethod" : "GET",
     "uri" : "http://petstore-demo-endpoint.execute-api.com/petstore/pets"
   }
   ```

   In the request payload, `uri` points to the backend endpoint associated with the method\. The `type` refers to the integration type\. For the specified HTTP endpoint `[http://petstore-demo-endpoint.execute-api.com/petstore/pets](http://petstore-demo-endpoint.execute-api.com/petstore/pets)`, the integration type must be `HTTP`\. The `httpMethod` property refers to the HTTP verb as required by the backend, which may be different from the method request HTTP verb set when calling the `method:put` link\-relation\.

   The successful request returns a response of a `201 Created` status code with a payload similar to the following output:

   ```
   {
     ...,
     "cacheKeyParameters": [],
     "cacheNamespace": "47rxl6",
     "httpMethod": "GET",
     "passthroughBehavior": "WHEN_NO_MATCH",
     "type": "HTTP",
     "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/pets"
   }
   ```

   Similarly, to set up the integration for the `GET /pets/{petId}` method, invoke the following `integration:put` link\-relation of API Gateway, referencing the `/pets/{petId}` resource by its value of `ab34fgd`, and adding the request parameter mapping from `{petId}` to `{id}`:

   ```
   PUT /restapis/x7hyqq0ik7/resources/ab34fgd/methods/GET/integration HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T002249Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=10359971a8c54862a47e39d6a6e4b6e62c263e9a2b785b47b40c426c0aa61c19
   
   {
     "type" : "HTTP",
     "httpMethod" : "GET",
     "uri" : "http://petstore-demo-endpoint.execute-api.com/petstore/pets/{id}",
     "requestParameters": {
       "integration.request.path.id": "method.request.path.petId"
     }
   }
   ```

   The successful response of this `integration:put` request is shown as follows:

   ```
   {
     ...,
     "cacheKeyParameters": [],
     "cacheNamespace": "ab34fgd",
     "httpMethod": "GET",
     "passthroughBehavior": "WHEN_NO_MATCH",
     "type": "HTTP",
     "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/pets/{id}",
     "requestParameters": {
       "integration.request.path.id": "method.request.path.petId"
     }
   }
   ```

1. To set up the 200 OK `IntegrationResponse` for the `GET /pets` method, invoke the following [integrationresponse:put](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/integrationresponse-put/) link\-relation of API Gateway: 

   ```
   PUT /restapis/x7hyqq0ik7/resources/47rxl6/methods/GET/integration/responses/200 HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T004542Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=545ab6ea151f72c52161ee856ee621f136d717e40a02743cd8fe3638895794b1
   Cache-Control: no-cache
   Postman-Token: d29cabea-5232-7021-88ad-f1e950f55a99
   
   {}
   ```

   The successful request returns a response of the `201 Created` status code and a payload similar to the following output:

   ```
   {
     ...,
     "statusCode": "200"
   }
   ```

   To set up the 200 OK `IntegrationResponse` for the `GET /pets/{petId}` \(the resource `id` is `ab34fgd`\) method, invoke the following [integrationresponse:put](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/integrationresponse-put/) link\-relation of API Gateway: 

   ```
   PUT /restapis/x7hyqq0ik7/resources/ab34fgd/methods/GET/integration/responses/200 HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T004542Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=545ab6ea151f72c52161ee856ee621f136d717e40a02743cd8fe3638895794b1
   
   {}
   ```

   We now have successfully created a simple PetStore API with the `GET /pets` and `GET /pets/{petId}` method with the `HTTP` integration type\. 

1. To open the API for your customers to call, deploy the API to a `test` stage by invoking [deployment:create](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create)\) link\-relation of API Gateway\. 

   ```
   POST /restapis/x7hyqq0ik7/deployments HTTP/1.1
   Host: apigateway.us-west-2.amazonaws.com
   Content-Type: application/x-amz-json-1.0
   X-Amz-Date: 20170512T004542Z
   Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170512/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=545ab6ea151f72c52161ee856ee621f136d717e40a02743cd8fe3638895794b1
   
   {
   	"stageName" : "test",
   	"stageDescription" : "First stage",
   	"description" : "First deployment"
   }
   ```

   The successful response has a status code of `201 Created` and a payload similar to the following: 

   ```
   {
       ...,
       "createdDate": "2017-10-13T20:28:56Z",
       "description": "First deployment",
       "id": "s7ja1r"
   }
   ```

    Now, you can test the deployed API by typing the `https://x7hyqq0ik7.execute-api.us-west-2.amazonaws.com/test/pets` URL, for the GET /pets method, in a browser\. Substitute the `RestApi` identifier \(`x7hyqq0ik7`\) with the identifier of your API\. The expected output should be as follows: 

   ```
   [
     {
       "id": 1,
       "type": "dog",
       "price": 249.99
     },
     {
       "id": 2,
       "type": "cat",
       "price": 124.99
     },
     {
       "id": 3,
       "type": "fish",
       "price": 0.99
     }
   ]
   ```

    To test the `GET /pets/{petId}` method, type the `https://x7hyqq0ik7.execute-api.us-west-2.amazonaws.com/test/pets/3` URL in the browser, replacing the `RestApi` identifier with the identifier of your API\. The expected output should be like this: 

   ```
   {
     "id": 3,
     "type": "fish",
     "price": 0.99
   }
   ```