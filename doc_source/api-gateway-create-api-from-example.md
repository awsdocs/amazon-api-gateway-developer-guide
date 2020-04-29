# Tutorial: Create a REST API by importing an example<a name="api-gateway-create-api-from-example"></a>

You can use the Amazon API Gateway console to create and test a simple REST API with the HTTP integration for a PetStore website\. The API definition is preconfigured as a OpenAPI 2\.0 file\. After loading the API definition into API Gateway, you can use the API Gateway console to examine the API's basic structure or simply deploy and test the API\. 

 The PetStore example API supports the following methods for a client to access the HTTP backend website of `http://petstore-demo-endpoint.execute-api.com/petstore/pets`\. 
+ `GET /`: for read access of the API's root resource that is not integrated with any backend endpoint\. API Gateway responds with an overview of the PetStore website\. This is an example of the `MOCK` integration type\.
+ `GET /pets`: for read access to the API's `/pets` resource that is integrated with the like\-named backend `/pets` resource\. The backend returns a page of available pets in the PetStore\. This is an example of the `HTTP` integration type\. The URL of the integration endpoint is `http://petstore-demo-endpoint.execute-api.com/petstore/pets`\.
+ `POST /pets`: for write access to the API's `/pets` resource that is integrated with the backend `/petstore/pets` resource\. Upon receiving a correct request, the backend adds the specified pet to the PetStore and returns the result to the caller\. The integration is also `HTTP`\.
+ `GET /pets/{petId}`: for read access to a pet as identified by a `petId` value as specified as a path variable of the incoming request URL\. This method also has the `HTTP` integration type\. The backend returns the specified pet found in the PetStore\. The URL of the backend HTTP endpoint is `http://petstore-demo-endpoint.execute-api.com/petstore/pets/n`, where `n` is an integer as the identifier of the queried pet\.

 The API supports CORS access via the `OPTIONS` methods of the `MOCK` integration type\. API Gateway returns the required headers supporting CORS access\. 

The following procedure walks you through the steps to create and test an API from an example using the API Gateway Console\.

**To import, build, and test the example API**

1. If you haven't already done so, complete the steps in [Prerequisites: Get ready to build an API in API Gateway](setting-up.md)\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If this is your first time using API Gateway, you see a page that introduces you to the features of the service\. Under **REST API**, choose **Build**\. When the **Create Example API** popup appears, choose **OK**\.

   If this is not your first time using API Gateway, choose **Create API**\. Under **REST API**, choose **Build**\.

1.  Under **Create new API**, choose **Example API** and then choose **Import** to create the example API\. For your first API, the API Gateway console starts with this option as default\.   
![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-by-importing-example.png)

    You can scroll down the OpenAPI definition for details of this example API before choosing **Import**\. 

1. The newly created API is shown as follows:  
![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-by-importing-example-result.png)

    The **Resources** pane shows the structure of the created API as a tree of nodes\. API methods defined on each resource are edges of the tree\. When a resource is selected, all of its methods are listed in the **Methods** pane on the right\. Displayed under each method is a brief summary of the method, including its endpoint URL, authorization type, and API Key requirement\. 

1.  To view the details of a method, to modify its set\-up, or to test the method invocation, choose the method name from either the method list or the resource tree\. Here, we choose the `POST /pets` method as an illustration:   
![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-by-importing-example-post-method-execution.png)

    The resulting **Method Execution** pane presents a logical view of the chosen \(`POST /pets`\) method's structure and behaviors: **Method Request** and **Method Response** are the API's interface with the API's frontend \(a client\), whereas **Integration Request** and **Integration Response** are the API's interface with the backend \(`http://petstore-demo-endpoint.execute-api.com/petstore/pets`\)\. A client uses the API to access a backend feature through the **Method Request**\. API Gateway translates the client request, if necessary, into the form acceptable to the backend in **Integration Request** before forwarding the incoming request to the backend\. The transformed request is known as the integration request\. Similarly, the backend returns the response to API Gateway in **Integration Response**\. API Gateway then routes it to **Method Response** before sending it to the client\. Again, if necessary, API Gateway can map the backend response data to a form expected by the client\. 

    For the `POST` method on an API resource, the method request payload can be passed through to the integration request without modification, if the method request's payload is of the same format as the integration request's payload\. 

   The `GET /` method request uses the `MOCK` integration type and is not tied to any real backend endpoint\. The corresponding **Integration Response** is set up to return a static HTML page\. When the method is called, the API Gateway simply accepts the request and immediately returns the configured integration response to the client by way of **Method Response**\. You can use the mock integration to test an API without requiring a backend endpoint\. You can also use it to serve a local response, generated from a response body\-mapping template\. 

   As an API developer, you control the behaviors of your API's frontend interactions by configuring the method request and a method response\. You control the behaviors of your API's backend interactions by setting up the integration request and integration response\. These involve data mappings between a method and its corresponding integration\. We cover the method setup in [Tutorial: Build a REST API with HTTP non\-proxy integration](api-gateway-create-api-step-by-step.md)\. For now, we focus on testing the API to provide an end\-to\-end user experience\. 

1.  Choose **Test** shown on **Client** \(as shown in the previous image\) to start testing\. For example, to test the `POST /pets` method, enter the following `{"type": "dog","price": 249.99}` payload into the **Request Body** before choosing the **Test** button\.   
![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-by-importing-example-post-method-test.png)

    The input specifies the attributes of the pet that we want to add to the list of pets on the PetStore website\. 

1. The results display as follows:  
![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-by-importing-example-post-method-test-result.png)

    The **Logs** entry of the output shows the state changes from the method request to the integration request, and from the integration response to the method response\. This can be useful for troubleshooting any mapping errors that cause the request to fail\. In this example, no mapping is applied: the method request payload is passed through the integration request to the backend and, similarly, the backend response is passed through the integration response to the method response\. 

    To test the API using a client other than the API Gateway test\-invoke\-request feature, you must first deploy the API to a stage\. 

1.  To deploy the sample API, select the **PetStore** API, and then choose **Deploy API** from the **Actions** menu\.   
![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-by-importing-example-deploy-api.png)

    In **Deploy API**, for **Deployment stage**, choose **\[New Stage\]** because this is the first deployment of the API\. Type a name \(e\.g\., **test**\) in **Stage name** and, optionally, type descriptions in **Stage description** and **Deployment description**\. Choose **Deploy**\.   
![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-by-importing-example-deploy-create.png)

    In the resulting **Stage Editor** pane, **Invoke URL** displays the URL to invoke the API's `GET /` method request\. 

1. On **Stage Editor**, follow the **Invoke URL** link to submit the `GET /` method request in a browser\. A successful response return the result, generated from the mapping template in the integration response\. 

1.  In the **Stages** navigation pane, expand the **test** stage, select **GET** on `/pets/{petId}`, and then copy the **Invoke URL** value of `https://api-id.execute-api.region.amazonaws.com/test/pets/{petId}`\. `{petId}` stands for a path variable\. 

    Paste the **Invoke URL** value \(obtained in the previous step\) into the address bar of a browser, replacing `{petId}` by, for example, `1`, and press Enter to submit the request\. A 200 OK response should return with the following JSON payload: 

   ```
   {
     "id": 1,
     "type": "dog",
     "price": 249.99
   }
   ```

    Invoking the API method as shown is possible because its **Authorization** type is set to `NONE`\. If the `AWS_IAM` authorization were used, you would sign the request using the [Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/sigv4_signing.html) \(SigV4\) protocols\. For an example of such a request, see [Tutorial: Build a REST API with HTTP non\-proxy integration](api-gateway-create-api-step-by-step.md)\. 