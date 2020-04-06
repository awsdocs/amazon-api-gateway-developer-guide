# Set up an edge\-optimized API using AWS CLI commands<a name="create-api-using-awscli"></a>

Setting up an API using the AWS CLI requires working with the `[create\-rest\-api](https://docs.aws.amazon.com/cli/latest/reference/apigateway/create-rest-api.html)`, `[create\-resource](https://docs.aws.amazon.com/cli/latest/reference/apigateway/create-resource.html)` or `[get\-resources](https://docs.aws.amazon.com/cli/latest/reference/apigateway/get-resources.html)`, `[put\-method](https://docs.aws.amazon.com/cli/latest/reference/apigateway/put-method.html)`, `[put\-method\-response](https://docs.aws.amazon.com/cli/latest/reference/apigateway/put-method-response.html)`, `[put\-integration](https://docs.aws.amazon.com/cli/latest/reference/apigateway/put-integration.html)`, and `[put\-integration\-response](https://docs.aws.amazon.com/cli/latest/reference/apigateway/put-integration-response.html)` commands\. The following procedures show how to work with these AWS CLI commands to create the simple PetStore API of the `HTTP` integration type\. 

**To create a simple PetStore API using AWS CLI**

1.  Call the `create-rest-api` command to set up the `RestApi` in a specific region \(`us-west-2`\)\. 

   ```
   aws apigateway create-rest-api --name 'Simple PetStore (AWS CLI)' --region us-west-2
   ```

   The following is the output of this command:

   ```
   {
       "name": "Simple PetStore (AWS CLI)", 
       "id": "vaz7da96z6", 
       "createdDate": 1494572809
   }
   ```

   Note the returned `id` of the newly created `RestApi`\. You need it to set up other parts of the API\.

1.  Call the `get-resources` command to retrieve the root resource identifier of the `RestApi`\. 

   ```
   aws apigateway get-resources --rest-api-id vaz7da96z6 --region us-west-2
   ```

   The following is the output of this command:

   ```
   {
       "items": [
           {
               "path": "/", 
               "id": "begaltmsm8"
           }
       ]
   }
   ```

   Note the root resource `Id`\. You need it to start setting the API's resource tree and configuring methods and integrations\.

1.  Call the `create-resource` command to append a child resource \(`pets`\) under the root resource \(`begaltmsm8`\):

   ```
   aws apigateway create-resource --rest-api-id vaz7da96z6 \
         --region us-west-2 \
         --parent-id begaltmsm8 \
         --path-part pets
   ```

   The following is the output of this command:

   ```
   {
       "path": "/pets", 
       "pathPart": "pets", 
       "id": "6sxz2j", 
       "parentId": "begaltmsm8"
   }
   ```

   To append a child resource under the root, you specify the root resource `Id` as the `parentId` property value\. Similarly, to append a child resource under the `pets` resource, you repeat the preceding step while replacing the `parent-id` value with the `pets` resource `id` of `6sxz2j`:

   ```
   aws apigateway create-resource --rest-api-id vaz7da96z6 \
         --region us-west-2 \
         --parent-id 6sxz2j \
         --path-part '{petId}'
   ```

   To make a path part a path parameter, enclose it in a pair of curly brackets\. If successful, this command returns the following response:

   ```
   {
       "path": "/pets/{petId}", 
       "pathPart": "{petId}", 
       "id": "rjkmth", 
       "parentId": "6sxz2j"
   }
   ```

   Now that you created two resources: `/pets` \(`6sxz2j`\) and `/pets/{petId}` \(`rjkmth`\), you can proceed to set up methods on them\.

1. Call the `put-method` command to add the `GET` HTTP method on the `/pets` resource\. This creates an API `Method` of `GET /pets` with open access, referencing the `/pets` resource by its ID value of `6sxz2j`\. 

   ```
   aws apigateway put-method --rest-api-id vaz7da96z6 \
          --resource-id 6sxz2j \
          --http-method GET \
          --authorization-type "NONE" \
          --region us-west-2
   ```

   The following is the successful output of this command:

   ```
   {
       "apiKeyRequired": false, 
       "httpMethod": "GET", 
       "authorizationType": "NONE"
   }
   ```

   The method is for open access because `authorization-type` is set to `NONE`\. To permit only authenticated users to call the method, you can use IAM roles and policies, a Lambda authorizer \(formerly known as a custom authorizer\), or an Amazon Cognito user pool\. For more information, see [Controlling and managing access to a REST API in API Gateway](apigateway-control-access-to-api.md)\.

   To enable read access to the `/pets/{petId}` resource \(`rjkmth`\), add the `GET` HTTP method on it to create an API `Method` of `GET /pets/{petId}` as follows\. 

   ```
   aws apigateway put-method --rest-api-id vaz7da96z6 \
          --resource-id rjkmth --http-method GET \
          --authorization-type "NONE" \
          --region us-west-2 \
          --request-parameters method.request.path.petId=true
   ```

   The following is the successful output of this command: 

   ```
   {
       "apiKeyRequired": false, 
       "httpMethod": "GET", 
       "authorizationType": "NONE", 
       "requestParameters": {
           "method.request.path.petId": true
       }
   }
   ```

   Note that the method request path parameter of `petId` must be specified as a required request parameter for its dynamically set value to be mapped to a corresponding integration request parameter and passed to the backend\. 

1.  Call the `put-method-response` command to set up the 200 OK response of the `GET /pets` method, specifying the `/pets` resource by its ID value of `6sxz2j`\. 

   ```
   aws apigateway put-method-response --rest-api-id vaz7da96z6 \ 
          --resource-id 6sxz2j --http-method GET \
          --status-code 200  --region us-west-2
   ```

   The following is the output of this command:

   ```
   {
       "statusCode": "200"
   }
   ```

   Similarly, to set the 200 OK response of the `GET /pets/{petId}` method, do the following, specifying the `/pets/{petId}` resource by its resource ID value of `rjkmth`:

   ```
   aws apigateway put-method-response --rest-api-id vaz7da96z6 \
          --resource-id rjkmth --http-method GET \
          --status-code 200  --region us-west-2
   ```

   Having set up a simple client interface for the API, you can proceed to set up the integration of the API methods with the backend\. 

1.  Call the `put-integration` command to set up an `Integration` with a specified HTTP endpoint for the `GET /pets` method\. The `/pets` resource is identified by its resource Id `6sxz2j`: 

   ```
   aws apigateway put-integration --rest-api-id vaz7da96z6 \
          --resource-id 6sxz2j --http-method GET --type HTTP \
          --integration-http-method GET \
          --uri 'http://petstore-demo-endpoint.execute-api.com/petstore/pets' \
          --region us-west-2
   ```

   The following is the output of this command:

   ```
   {
       "httpMethod": "GET", 
       "passthroughBehavior": "WHEN_NO_MATCH", 
       "cacheKeyParameters": [], 
       "type": "HTTP", 
       "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/pets", 
       "cacheNamespace": "6sxz2j"
   }
   ```

   Notice that the integration `uri` of `http://petstore-demo-endpoint.execute-api.com/petstore/pets` specifies the integration endpoint of the `GET /pets` method\. 

   Similarly, you create an integration request for the `GET /pets/{petId}` method as follows: 

   ```
   aws apigateway put-integration \
           --rest-api-id vaz7da96z6 \ 
           --resource-id rjkmth \
           --http-method GET \
           --type HTTP \
           --integration-http-method GET \
           --uri 'http://petstore-demo-endpoint.execute-api.com/petstore/pets/{id}' \ 
           --request-parameters '{"integration.request.path.id":"method.request.path.petId"}' \
           --region us-west-2
   ```

   Here, the integration endpoint, `uri` of `http://petstore-demo-endpoint.execute-api.com/petstore/pets/{id}`, also uses a path parameter \(`id`\)\. Its value is mapped from the corresponding method request path parameter of `{petId}`\. The mapping is defined as part of the `request-parameters`\. If this mapping is not defined here, the client gets an error response when trying to call the method\. 

   The following is the output of this command:

   ```
   {
       "passthroughBehavior": "WHEN_NO_MATCH", 
       "cacheKeyParameters": [], 
       "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/pets/{id}", 
       "httpMethod": "GET", 
       "cacheNamespace": "rjkmth", 
       "type": "HTTP", 
       "requestParameters": {
           "integration.request.path.id": "method.request.path.petId"
       }
   }
   ```

1.  Call the `put-integration-response` command to create an `IntegrationResponse` of the `GET /pets` method integrated with an HTTP backend\. 

   ```
   aws apigateway put-integration-response --rest-api-id vaz7da96z6 \
          --resource-id 6sxz2j --http-method GET \
          --status-code 200 --selection-pattern ""  \
          --region us-west-2
   ```

   The following is the output of this command:

   ```
   {
       "selectionPattern": "", 
       "statusCode": "200"
   }
   ```

   Similarly, call the following `put-integration-response` command to create an `IntegrationResponse` of the `GET /pets/{petId}` method:

   ```
   aws apigateway put-integration-response --rest-api-id vaz7da96z6 \
         --resource-id rjkmth --http-method GET 
         --status-code 200 --selection-pattern ""  
         --region us-west-2
   ```

   With the preceding steps, you finished setting up a simple API that allows your customers to query available pets on the PetStore website and to view an individual pet of a specified identifier\. To make it callable by your customer, you must deploy the API\.

1.  Deploy the API to a `stage` stage, for example, by calling `create-deployment`: 

   ```
   aws apigateway create-deployment --rest-api-id vaz7da96z6 \ 
          --region us-west-2 \
          --stage-name test \
          --stage-description 'Test stage' \
          --description 'First deployment'
   ```

You can test this API by typing the `https://vaz7da96z6.execute-api.us-west-2.amazonaws.com/test/pets` URL in a browser, and substituting `vaz7da96z6` with the identifier of your API\. The expected output should be as follows: 

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

To test the `GET /pets/{petId}` method, type `https://vaz7da96z6.execute-api.us-west-2.amazonaws.com/test/pets/3` in the browser\. You should receive the following response:

```
{
  "id": 3,
  "type": "fish",
  "price": 0.99
}
```