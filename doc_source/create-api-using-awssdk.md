# Set up an edge\-optimized API using the AWS SDK for Node\.js<a name="create-api-using-awssdk"></a>

 As an illustration, we use AWS SDK for Node\.js to describe how you can use an AWS SDK to create an API Gateway API\. For more information using an AWS SDK, including how to set up the development environment, see [AWS SDKs](https://aws.amazon.com/tools/)\. 

 Setting up an API using the AWS SDK for Node\.js involves calling the `[createRestApi](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/APIGateway.html#createRestApi-property)`, `[createResource](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/APIGateway.html#createResource-property)` or `[getResources](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/APIGateway.html#getResources-property)`, `[putMethod](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/APIGateway.html#putMethod-property)`, `[putMethodResponse](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/APIGateway.html#putMethodResponse-property)`, `[putIntegration](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/APIGateway.html#putIntegration-property)`, and `[putIntegrationResponse](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/APIGateway.html#putIntegrationResponse-property)` functions\. 

The following procedures walk you through the essential steps to use these SDK commands to set up a simple PetStore API supporting the `GET /pets` and `GET /pets/{petId}` methods\.

**To set up a simple PetStore API using the AWS SDK for Node\.js**

1. Instantiate the SDK:

   ```
   var AWS = require('aws-sdk');
   
   AWS.config.region = 'us-west-2';
   var apig = new AWS.APIGateway({apiVersion: '2015/07/09'});
   ```

1.  Call the `createRestApi` function to set up the `RestApi` entity\. 

   ```
   apig.createRestApi({
   	name: "Simple PetStore (node.js SDK)",
   	binaryMediaTypes: [
   		'*'
   	],
   	description: "Demo API created using the AWS SDK for node.js",
   	version: "0.00.001"
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else {
   		console.log('Create API failed:\n', err);
   	}
   });
   ```

   The function returns an output similar to the following result:

   ```
   {
     id: 'iuo308uaq7',
     name: 'PetStore (node.js SDK)',
     description: 'Demo API created using the AWS SDK for node.js',
     createdDate: 2017-09-05T19:32:35.000Z,
     version: '0.00.001',
     binaryMediaTypes: [ '*' ] 
   }
   ```

   The resulting API's identifier is `iuo308uaq7`\. You need to supply this to continue the setup of the API\.

1.  Call the `getResources` function to retrieve the root resource identifier of the `RestApi`\. 

   ```
   apig.getResources({
   	restApiId: 'iuo308uaq7'
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else {
   		console.log('Get the root resource failed:\n', err);
   	}
   })
   ```

   This function returns an output similar to the following result:

   ```
   {
       "items": [
           {
               "path": "/", 
               "id": "s4fb0trnk0"
           }
       ]
   }
   ```

   The root resource identifier is `s4fb0trnk0`\. This is the starting point for you to build the API resource tree, which you do next\.

1.  Call the `createResource` function to set up the `/pets` resource for the API, specifying the root resource identifier \(`s4fb0trnk0`\) on the `parentId` property\.

   ```
   apig.createResource({
   	restApiId: 'iuo308uaq7',
   	parentId: 's4fb0trnk0',
   	pathPart: 'pets'
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else {
   		console.log("The '/pets' resource setup failed:\n", err);
   	}	
   })
   ```

   The successful result is as follows:

   ```
   {
       "path": "/pets", 
       "pathPart": "pets", 
       "id": "8sxa2j", 
       "parentId": "s4fb0trnk0'"
   }
   ```

   To set up the `/pets/{petId}` resource, call the following `createResource` function, specifying the newly created `/pets` resource \(`8sxa2j`\) on the `parentId` property\. 

   ```
   apig.createResource({
   	restApiId: 'iuo308uaq7',
   	parentId: '8sxa2j',
   	pathPart: '{petId}'
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else {
   		console.log("The '/pets/{petId}' resource setup failed:\n", err);
   	}	
   })
   ```

   The successful result returns the newly created resource `id` value:

   ```
   {
       "path": "/pets/{petId}", 
       "pathPart": "{petId}", 
       "id": "au5df2", 
       "parentId": "8sxa2j"
   }
   ```

   Throughout this procedure, you refer to the `/pets` resource by specifying its resource ID of `8sxa2j`, and the `/pets/{petId}` resource by specifying its resource ID of `au5df2`\.

1.  Call the `putMethod` function to add the `GET` HTTP method on the `/pets` resource \(`8sxa2j`\)\. This sets up the `GET /pets` `Method` with open access\. 

   ```
   apig.putMethod({
   	restApiId: 'iuo308uaq7',
   	resourceId: '8sxa2j,
   	httpMethod: 'GET',
   	authorizationType: 'NONE'
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else {
   		console.log("The 'GET /pets' method setup failed:\n", err);
   	}	
   })
   ```

   This function returns an output similar to the following result:

   ```
   {
       "apiKeyRequired": false, 
       "httpMethod": "GET", 
       "authorizationType": "NONE"
   }
   ```

   To add the `GET` HTTP method on the `/pets/{petId}` resource \(`au5df2`\), which sets up the API method of `GET /pets/{petId}` with open access, call the `putMethod` function as follows\. 

   ```
   apig.putMethod({
   	restApiId: 'iuo308uaq7',
   	resourceId: 'au5df2',
   	httpMethod: 'GET',
   	authorizationType: 'NONE',
         requestParameters: {
           "method.request.path.petId" : true
         }
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else {
   		console.log("The 'GET /pets/{petId}' method setup failed:\n", err);
   	}	
   })
   ```

   This function returns an output similar to the following result:

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

   You need to set the `requestParameters` property as shown in the preceding example to map and pass the client\-supplied `petId` value to the backend\.

1.  Call the `putMethodResponse` function to set up a method response for the `GET /pets` method\. 

   ```
   apig.putMethodResponse({
   	restApiId: 'iuo308uaq7',
   	resourceId: "8sxa2j",
   	httpMethod: 'GET',
   	statusCode: '200'
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else {
   		console.log("Set up the 200 OK response for the 'GET /pets' method failed:\n", err);
   	}
   
   })
   ```

   This function returns an output similar to the following result:

   ```
   {
       "statusCode": "200"
   }
   ```

   To set the 200 OK response of the `GET /pets/{petId}` method, call the `putMethodResponse` function, specifying the `/pets/{petId}` resource identifier \(`au5df2`\) on the `resourceId` property\. 

   ```
   apig.putMethodResponse({
   	restApiId: 'iuo308uaq7',
   	resourceId: "au5df2",
   	httpMethod: 'GET',
   	statusCode: '200'
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else {
   		console.log("Set up the 200 OK response for the 'GET /pets/{petId}' method failed:\n", err);
   	}
   
   })
   ```

1.  Call the `putIntegration` function to set up the `Integration` with a specified HTTP endpoint for the `GET /pets` method, supplying the `/pets` resource identifier \(`8sxa2j`\) on the `parentId` property\. 

   ```
   apig.putIntegration({
   	restApiId: 'iuo308uaq7',
   	resourceId: '8sxa2j',
   	httpMethod: 'GET',
   	type: 'HTTP',
   	integrationHttpMethod: 'GET',
   	uri: 'http://perstore-demo-endpoint.execute-api.com/pets'
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else {
   		console.log("Set up the integration of the 'GET /' method of the API failed:\n", err);
   	}
   
   })
   ```

   This function returns an output similar the following:

   ```
   {
       "httpMethod": "GET", 
       "passthroughBehavior": "WHEN_NO_MATCH", 
       "cacheKeyParameters": [], 
       "type": "HTTP", 
       "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/pets", 
       "cacheNamespace": "8sxa2j"
   }
   ```

   To set up the integration of the `GET /pets/{petId}` method with the HTTP endpoint of `http://perstore-demo-endpoint.execute-api.com/pets/{id}` of the backend, call the following `putIntegration` function, supplying the API's `/pets/{petId}` resource identifier \(`au5df2`\) on the `parentId` property\. 

   ```
   apig.putIntegration({
   	restApiId: 'iuo308uaq7',
   	resourceId: 'au5df2',
   	httpMethod: 'GET',
   	type: 'HTTP',
   	integrationHttpMethod: 'GET',
   	uri: 'http://perstore-demo-endpoint.execute-api.com/pets/{id}',
          requestParameters: {
             "integration.request.path.id": "method.request.path.petId"
          }
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else {
   		console.log("The 'GET /pets/{petId}' method integration setup failed:\n", err);
   	}
   
   })
   ```

   This function returns a successful output similar to the following:

   ```
   {
       "httpMethod": "GET", 
       "passthroughBehavior": "WHEN_NO_MATCH", 
       "cacheKeyParameters": [], 
       "type": "HTTP", 
       "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/pets/{id}", 
       "cacheNamespace": "au5df2",
       "requestParameters": {
          "integration.request.path.id": "method.request.path.petId"
       }
   }
   ```

1.  Call the `putIntegrationResponse` function to set up the 200 OK integration response for the `GET /pets` method, specifying the API's `/pets` resource identifier \(`8sxa2j`\) on the `resourceId` property\. 

   ```
   apig.putIntegrationResponse({
   	restApiId: 'iuo308uaq7',
   	resourceId: '8sxa2j',
   	httpMethod: 'GET',
   	statusCode: '200',
   	selectionPattern: ''
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else
   		console.log("The 'GET /pets' method integration response setup failed:\n", err);
   
   })
   ```

   This function will return an output similar to the following result:

   ```
   {
       "selectionPattern": "", 
       "statusCode": "200"
   }
   ```

   To set up the 200 OK integration response of the `GET /pets/{petId}` method, call the `putIntegrationResponse` function, specifying the API's `/pets/{petId}` resource identifier \(`au5df2`\) on the `resourceId` property\. 

   ```
   apig.putIntegrationResponse({
   	restApiId: 'iuo308uaq7',
   	resourceId: 'au5df2',
   	httpMethod: 'GET',
   	statusCode: '200',
   	selectionPattern: ''
   }, function(err, data){
   	if (!err) {
   		console.log(data);
   	} else
   		console.log("The 'GET /pets/{petId}' method integration response setup failed:\n", err);
   
   })
   ```

1. As a good practice, test invoking the API before deploying it\. To test invoking the `GET /pets` method, call the `testInvokeMethod`, specifying the `/pets`resource identifier \(`8sxa2j`\) on the `resourceId` property:

   ```
   apig.testInvokeMethod({
   	restApiId: 'iuo308uaq7',
   	resourceId: '8sxa2j',
   	httpMethod: "GET",
   	pathWithQueryString: '/'
   }, function(err, data){
   	if (!err) {
   		console.log(data)
   	} else {
   		console.log('Test-invoke-method on 'GET /pets' failed:\n', err);
   	}
   })
   ```

   To test invoking the `GET /pets/{petId}` method, call the `testInvokeMethod`, specifying the `/pets/{petId}` resource identifier \(`au5df2`\) on the `resourceId` property:

   ```
   apig.testInvokeMethod({
   	restApiId: 'iuo308uaq7',
   	resourceId: 'au5df2',
   	httpMethod: "GET",
   	pathWithQueryString: '/'
   }, function(err, data){
   	if (!err) {
   		console.log(data)
   	} else {
   		console.log('Test-invoke-method on 'GET /pets/{petId}' failed:\n', err);
   	}
   })
   ```

1.  Finally, you can deploy the API for your customers to call\. 

   ```
   apig.createDeployment({
   	restApiId: 'iuo308uaq7',
   	stageName: 'test',
   	stageDescription: 'test deployment',
   	description: 'API deployment'
   }, function(err, data){
   	if (err) {
   		console.log('Deploying API failed:\n', err);
   	}	else {
   		console.log("Deploying API succeeded\n", data);
   	}
   })
   ```