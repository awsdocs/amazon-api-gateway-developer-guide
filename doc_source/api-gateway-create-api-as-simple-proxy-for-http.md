# Tutorial: Build a REST API with HTTP proxy integration<a name="api-gateway-create-api-as-simple-proxy-for-http"></a>

HTTP proxy integration is a simple, powerful, and versatile mechanism to build an API that allows a web application to access multiple resources or features of the integrated HTTP endpoint, for example the entire website, with a streamlined setup of a single API method\. In HTTP proxy integration, API Gateway passes the client\-submitted method request to the backend\. The request data that is passed through includes the request headers, query string parameters, URL path variables, and payload\. The backend HTTP endpoint or the web server parses the incoming request data to determine the response that it returns\. HTTP proxy integration makes the client and backend interact directly with no intervention from API Gateway after the API method is set up, except for known issues such as unsupported characters, which are listed in [Amazon API Gateway important notes](api-gateway-known-issues.md)\.

With the all\-encompassing proxy resource `{proxy+}`, and the catch\-all `ANY` verb for the HTTP method, you can use an HTTP proxy integration to create an API of a single API method\. The method exposes the entire set of the publicly accessible HTTP resources and operations of a website\. When the backend web server opens more resources for public access, the client can use these new resources with the same API setup\. To enable this, the website developer must communicate clearly to the client developer what the new resources are and what operations are applicable for each of them\.

As a quick introduction, the following tutorial demonstrates the HTTP proxy integration\. In the tutorial, we create an API using the API Gateway console to integrate with the PetStore website through a generic proxy resource `{proxy+}`, and create the HTTP method placeholder of `ANY`\. 

**Topics**
+ [Create an API with HTTP proxy integration using the API Gateway console](#api-gateway-create-api-as-simple-proxy-for-http-build)
+ [Test an API with HTTP proxy integration](#api-gateway-create-api-as-simple-proxy-for-http-test)

## Create an API with HTTP proxy integration using the API Gateway console<a name="api-gateway-create-api-as-simple-proxy-for-http-build"></a>

 The following procedure walks you through the steps to create and test an API with a proxy resource for an HTTP backend using the API Gateway console\. The HTTP backend is the `PetStore` website \(`http://petstore-demo-endpoint.execute-api.com/petstore/pets`\) from [Tutorial: Build a REST API with HTTP non\-proxy integration](api-gateway-create-api-step-by-step.md), in which screenshots are used as visual aids to illustrate the API Gateway UI elements\. If you are new to using the API Gateway console to create an API, you may want to follow that section first\. 

**To build an API with HTTP proxy integration with the PetStore website through a proxy resource**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If this is your first time using API Gateway, you see a page that introduces you to the features of the service\. Under **REST API**, choose **Build**\. When the **Create Example API** popup appears, choose **OK**\.

   If this is not your first time using API Gateway, choose **Create API**\. Under **REST API**, choose **Build**\.

   1. Choose **New API**\.

   1. Enter a name in **API Name**\.

   1. Optionally, add a brief description in **Description**\.

   1. Choose **Create API**\.

   For this tutorial, use `ProxyResourceForPetStore` for the API name\.

1. To create a child resource, choose a parent resource item under the **Resources** tree and then choose **Create Resource** from the **Actions** drop\-down menu\. Then, do the following in the **New Child Resource** pane\.

   1. Select the **Configure as proxy resource** option to create a proxy resource\. Otherwise, leave it de\-selected\.

   1. Type a name in the **Resource Name\*** input text field\.

   1. Type a new name or use the default name in the **Resource Path\*** input text field\.

   1. Choose **Create Resource**\.

   1. Select **Enable API Gateway CORS**, if required\.

   For this tutorial, select **Configure as proxy resource**\. For **Resource Name**, use the default, `proxy`\. For **Resource Path**, use `/{proxy+}`\. Select **Enable API Gateway CORS**\.  
![\[Create a child resource.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-simple-proxy-create-proxy-resource.png)

1. To set up the `ANY` method for integration with the HTTP backend, do the following:

   1. Choose the resource just created and then choose **Create Method** from the **Actions** drop\-down menu\.

   1. Choose `ANY` from the HTTP method drop\-down list and then choose the check mark icon to save the choice\.

   1. Choose **HTTP Proxy** for **Integration type**\.

   1. Type an HTTP backend resource URL in **Endpoint URL**\.

   1. Use default settings for other fields\.

   1. Choose **Save** to finish configuring the `ANY` method\.

   For this tutorial, use `http://petstore-demo-endpoint.execute-api.com/{proxy}` for the **Endpoint URL**\.  
![\[Set up the ANY method.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-simple-proxy-petstore-integrate-proxy-resource.png)

 In the API just created, the API's proxy resource path of `{proxy+}` becomes the placeholder of any of the backend endpoints under `http://petstore-demo-endpoint.execute-api.com/`\. For example, it can be `petstore`, `petstore/pets`, and `petstore/pets/{petId}`\. The `ANY` method serves as a placeholder for any of the supported HTTP verbs at run time\. 

## Test an API with HTTP proxy integration<a name="api-gateway-create-api-as-simple-proxy-for-http-test"></a>

 Whether a particular client request succeeds depends on the following: 
+  If the backend has made the corresponding backend endpoint available and, if so, has granted the required access permissions\. 
+ If the client supplies the correct input\.

For example, the PetStore API used here does not expose the `/petstore` resource\. As such, you get a `404 Resource Not Found` response containing the error message of `Cannot GET /petstore`\. 

In addition, the client must be able to handle the output format of the backend in order to parse the result correctly\. API Gateway does not mediate to facilitate interactions between the client and backend\. 

**To test an API integrated with the PetStore website using HTTP proxy integration through the proxy resource**

1. To use the API Gateway console to test invoking the API, do the following\.

   1. Choose **ANY** on a proxy resource in the **Resources** tree\.

   1. Choose **Test** in the **Method Execution** pane\.

   1. From the **Method** drop\-down list, choose an HTTP verb supported by the backend\.

   1. Under **Path**, type a specific path for the proxy resource supporting the chosen operation\.

   1. If required, type a supported query expression for the chosen operation under the **Query Strings** heading\.

   1. If required, type one or more supported header expressions for the chosen operation under the **Headers** heading\.

   1. If configured, set the required stage variable values for the chosen operation under the **Stage Variables** heading\.

   1. If prompted and required, choose an API Gateway\-generated client certificate under the **Client Certificate** heading to the operation to be authenticated by the back end\.

   1. If prompted, type an appropriate request body in the text editor under the **Request Body** heading\.

   1. Choose **Test** to test invoking the method\.

   For this tutorial, use `GET` for **Method** in place of `ANY`, use `petstore/pets` for **Path** in place of the proxy resource path \(**\{proxy\}**\), and `type=fish` for **Query Strings**\.  
![\[Test a method.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-simple-proxy-petstore-call-proxy-resource.png)

   Because the backend website supports the `GET /petstore/pets?type=fish` request, it returns a successful response similar to the following:

   ```
   [
     {
       "id": 1,
       "type": "fish",
       "price": 249.99
     },
     {
       "id": 2,
       "type": "fish",
       "price": 124.99
     },
     {
       "id": 3,
       "type": "fish",
       "price": 0.99
     }
   ]
   ```

   If you try to call `GET /petstore`, you get a `404` response with an error message of `Cannot GET /petstore`\. This is because the backend does not support the specified operation\. If you call `GET /petstore/pets/1`, you get a `200 OK` response with the following payload, because the request is supported by the PetStore website\.

   ```
   {
     "id": 1,
     "type": "dog",
     "price": 249.99
   }
   ```

1. To use a browser to call a GET method on a specific resource of the API, do the following\.

   1. If you have not done so, choose **Deploy API** from the **Actions** drop\-down menu for the API you created\. Follow the instructions to deploy the API to a specific stage\. Note the **Invoke URL** that displays on the resulting **Stage Editor** page\. This is the base URL of the API\.

   1. To submit a GET request on a specific resource, append the resource path, including possible query string expressions to the **Invoke URL** value obtained in the previous step, copy the complete URL into the address bar of a browser, and choose Enter\.

   For this tutorial, deploy the API to a `test` stage and append `petstore/pets?type=fish` to the API's Invoke URL\. This produces a URL of `https://4z9giyi2c1.execute-api.us-west-2.amazonaws.com/test/petstore/pets?type=fish`\.

   The result should be the same as returned when you use `TestInvoke` from the API Gateway console\.