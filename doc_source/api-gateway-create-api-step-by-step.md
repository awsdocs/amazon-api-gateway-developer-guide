# Tutorial: Build a REST API with HTTP non\-proxy integration<a name="api-gateway-create-api-step-by-step"></a>

 In this tutorial, you create an API from scratch using the Amazon API Gateway console\. You can think of the console as an API design studio and use it to scope the API features, to experiment with its behaviors, to build the API, and to deploy your API in stages\. 

**Topics**
+ [Create an API with HTTP custom integration](#api-gateway-create-resource-and-methods)
+ [Map request parameters for an API Gateway API](#getting-started-mappings)
+ [Map response payload](#getting-started-models)

## Create an API with HTTP custom integration<a name="api-gateway-create-resource-and-methods"></a>

 This section walks you through the steps to create resources, expose methods on a resource, configure a method to achieve the desired API behaviors, and to test and deploy the API\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If this is your first time using API Gateway, you see a page that introduces you to the features of the service\. Under **REST API**, choose **Build**\. When the **Create Example API** popup appears, choose **OK**\.

   If this is not your first time using API Gateway, choose **Create API**\. Under **REST API**, choose **Build**\.

   1. Choose **New API**\.

   1. Enter a name in **API Name**\.

   1. Optionally, add a brief description in **Description**\.

   1. Choose **Create API**\.

    As a result, an empty API is created\. The **Resources** tree shows the root resource \(`/`\) without any methods\. In this exercise, we will build the API with the HTTP custom integration of the PetStore website \(http://petstore\-demo\-endpoint\.execute\-api\.com/petstore/pets\.\) For illustration purposes, we will create a `/pets` resource as a child of the root and expose a GET method on this resource for a client to retrieve a list of available Pets items from the PetStore website\. 

1.  To create the `/pets` resource, select the root, choose **Actions** and then choose **Create Resource**\. 

    Type `Pets` in **Resource Name**, leave the **Resource Path** value as given, choose **Enable API Gateway CORS**, and choose **Create Resource**\.   
![\[Create a resource part b\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-step-by-step-create-resource-2.png)

1.  To expose a GET method on the `/pets` resource, choose **Actions** and then **Create Method**\.   
![\[Create method on a resource\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-step-by-step-create-method.png)

    Choose **GET** from the list under the **/pets** resource node and choose the check mark icon to finish creating the method\.   
![\[Create method on a resource\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-step-by-step-create-get-method.png)
**Note**  
Other options for an API method include:  
**POST**, primarily used to create child resources\.
**PUT**, primarily used to update existing resources \(and, although not recommended, can be used to create child resources\)\.
**DELETE**, used to delete resources\.
**PATCH**, used to update resources\.
**HEAD**, primarily used in testing scenarios\. It is the same as GET but does not return the resource representation\.
**OPTIONS**, which can be used by callers to get information about available communication options for the target service\.

    The method created is not yet integrated with the backend\. The next step sets this up\. 

1.  In the method's **Setup** pane, select **HTTP** for **Integration type**, select **GET** from the **HTTP method** drop\-down list, type **http://petstore\-demo\-endpoint\.execute\-api\.com/petstore/pets** as the **Endpoint URL** value, leave all other settings as default, and then choose **Save**\. 
**Note**  
 For the integration request's **HTTP method**, you must choose one supported by the backend\. For `HTTP` or `Mock integration`, it makes sense that the method request and the integration request use the same HTTP verb\. For other integration types the method request will likely use an HTTP verb different from the integration request\. For example, to call a Lambda function, the integration request must use `POST` to invoke the function, whereas the method request may use any HTTP verb depending on the logic of the Lambda function\.   
![\[Integrate GET on pets with PetStore site\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-step-by-step-integrate-get-on-pets.png)

    When the method setup finishes, you are presented with the **Method Execution** pane, where you can further configure the method request to add query string or custom header parameters\. You can also update the integration request to map input data from the method request to the format required by the back end\. 

    The PetStore website allows you to retrieve a list of `Pet` items by the pet type \(e\.g\., "Dog" or "Cat"\) on a given page\. It uses the `type` and `page` query string parameters to accept such input\. As such, we must add the query string parameters to the method request and map them into the corresponding query strings of the integration request\. 

1.  In the GET method's **Method Execution** pane, choose **Method Request**, select `AWS_IAM` for **Authorization**, expand the **URL Query String Parameters** section, and choose **Add query string** to create two query string parameters named `type` and `page`\. Choose the check mark icon to save each query string parameter as you add it\.   
![\[Add query strings to GET on pets method request\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-step-by-step-add-query-strings-for-get-on-pets.png)

    The client can now supply a pet type and a page number as query string parameters when submitting a request\. These input parameters must be mapped into the integration's query string parameters to forward the input values to our PetStore website in the backend\. Because the method uses `AWS_IAM`, you must sign the request to invoke the method\. 

1.  From the method's **Integration Request** page, expand the **URL Query String Parameters** section\. By default, the method request query string parameters are mapped to the like\-named integration request query string parameters\. This default mapping works for our demo API\. We will leave them as given\. To map a different method request parameter to the corresponding integration request parameter, choose the pencil icon for the parameter to edit the mapping expression, shown in the **Mapped from** column\. To map a method request parameter to a different integration request parameter, first choose the delete icon to remove the existing integration request parameter, choose **Add query string** to specify a new name and the desired method request parameter mapping expression\.   
![\[Map query strings to GET on pets from method request to integration request\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-step-by-step-map-query-strings-for-get-on-pets.png)

    This completes building the simple demo API\. It's time to test the API\. 

1.  To test the API using the API Gateway console, choose **Test** on the **Method Execution** pane for the `GET /pets` method\. In the **Method Test** pane, enter **Dog** and **2** for the **type** and **page** query strings, respectively, and then choose **Test**\. 

    The result is shown as follows\. \(You may need to scroll down to see the test result\.\)   
![\[Test-invoke GET on pets method result\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-step-by-step-test-invoke-get-on-pets-result.png)

    Now that the test is successful, we can deploy the API to make it publicly available\. 

1.  To deploy the API, select the API and then choose **Deploy API** from the **Actions** drop\-down menu\.   
![\[Deploy API\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-step-by-step-deploy-api.png)

    In the **Deploy API** dialog, choose a stage \(or `[New Stage]` for the API's first deployment\); enter a name \(e\.g\., "test", "prod", "dev", etc\.\) in the **Stage name** input field; optionally, provide a description in **Stage description** and/or **Deployment description**; and then choose **Deploy**\.   
![\[Deploy API part 2\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-create-api-step-by-step-deploy-api-2.png)

    Once deployed, you can obtain the invocation URLs \(**Invoke URL**\) of the API's endpoints\. 

    If the GET method supported open access, \(i\.e\., if the method's authorization type were set to `NONE`\) you could double\-click the **Invoke URL** link to invoke the method in your default browser\. If needed, you could also append necessary query string parameters to the invocation URL\. With the `AWS_IAM` authorization type described here, you must sign the request with an [access key ID and the corresponding secret key](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) of an IAM user of your AWS account\. To do this, you must use a client that supports the [Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/sigv4_signing.html) \(SigV4\) protocols\. An example of such a client is an app that uses one of the AWS SDKs or the [Postman](https://www.postman.com/) application or cURL commands\. To call a POST, PUT, or PATCH method that take a payload, you also need to use such a client to handle the payload\. 

    To invoke this API method in the Postman, append the query string parameters to the stage\-specific method invocation URL \(as shown in the previous image\) to create the complete method request URL: 

   ```
   https://api-id.execute-api.region.amazonaws.com/test/pets?type=Dog&page=2
   ```

    Specify this URL in the address bar of the browser\. Choose **GET** as the HTTP verb\. Select **AWS Signature** for the **Type** option under the **Authorization** tab, and then specify the following required properties before sending the request: 
   + For **AccessKey**, type the caller's AWS access key, as provisioned from [AWS IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)\.
   + For **SecretKey**, type the caller's AWS secret key, as provisioned from AWS IAM when the access key was first created\.
   + For **AWS Region**, type the API\-hosting AWS Region, as specified in the invocation URL\.
   + For **Service Name**, type `execute-api`, for the API Gateway execution service\. 

    If you use an SDK to create a client, you can call the methods exposed by the SDK to sign the request\. For implementation details, see the [AWS SDK](https://aws.amazon.com/tools/) of your choosing\. 
**Note**  
 When changes are made to your API, you must redeploy the API to make the new or updated features available before invoking the request URL again\. 

## Map request parameters for an API Gateway API<a name="getting-started-mappings"></a>

 In this walkthrough, we describe how to map method request parameters to the corresponding integration request parameters for an API Gateway API\. We create an example API with the HTTP custom integration and use it to demonstrate how to use API Gateway to map a method request parameter to the corresponding integration request parameter\. We then access the following publicly accessible HTTP endpoint: 

```
http://petstore-demo-endpoint.execute-api.com/petstore/pets
```

 If you copy the above URL, paste it into the address bar of a web browser, and press `Enter` or `Return`, you get the following JSON\-formatted response body: 

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

The preceding endpoint can take two query parameters: `type` and `page`\. For example, change the URL to the following:

```
http://petstore-demo-endpoint.execute-api.com/petstore/pets?type=cat&page=2
```

You receive the following JSON\-formatted response payload, displaying page 2 of only the cats:

```
[
  {
    "id": 4,
    "type": "cat",
    "price": 999.99
  },
  {
    "id": 5,
    "type": "cat",
    "price": 249.99
  },
  {
    "id": 6,
    "type": "cat",
    "price": 49.97
  }
]
```

This endpoint also supports the use of an item ID, as expressed by a URL path parameter\. For example, browse to the following:

```
http://petstore-demo-endpoint.execute-api.com/petstore/pets/1
```

The following JSON\-formatted information about the item with an ID of 1 is displayed:

```
{
  "id": 1,
  "type": "dog",
  "price": 249.99
}
```

In addition to supporting GET operations, this endpoint takes POST requests with a payload\. For example, use [Postman](https://www.postman.com/) to send a POST method request to the following:

```
http://petstore-demo-endpoint.execute-api.com/petstore/pets
```

Include the header `Content-type: application/json` and the following request body:

```
{
  "type": "dog",
  "price": 249.99
}
```

You receive the following JSON object in the response body:

```
{
  "pet": {
    "type": "dog",
    "price": 249.99
  },
  "message": "success"
}
```

 We now expose these and other features by building an API Gateway API with the HTTP custom integration of this PetStore website\. The tasks include the following:
+  Create an API with a resource of `https://my-api-id.execute-api.region-id.amazonaws.com/test/petstorewalkthrough/pets` acting as a proxy to the HTTP endpoint of `http://petstore-demo-endpoint.execute-api.com/petstore/pets`\. 
+ Enable the API to accept two method request query parameters of `petType` and `petsPage`, map them to the `type` and `page` query parameters of the integration request, respectively, and pass the request to the HTTP endpoint\.
+ Support a path parameter of `{petId}` on the API's method request URL to specify an item ID, map it to the `{id}` path parameter in the integration request URL, and send the request to the HTTP endpoint\.
+ Enable the method request to accept the JSON payload of the format defined by the backend website, and pass the payload without modification through the integration request to the backend HTTP endpoint\. 

**Topics**
+ [Prerequisites](#getting-started-mappings-prerequisites)
+ [Step 1: Create resources](#getting-started-mappings-add-resources)
+ [Step 2: Create and test the methods](#getting-started-mappings-set-methods)
+ [Step 3: Deploy the API](#getting-started-mappings-deploy)
+ [Step 4: Test the API](#getting-started-mappings-test)

**Note**  
 Pay attention to the casing used in the steps of this walkthrough\. Typing a lowercase letter instead of an uppercase letter \(or vice versa\) can cause errors later in the walkthrough\. 

### Prerequisites<a name="getting-started-mappings-prerequisites"></a>

Before you begin this walkthrough, you should do the following:

1. Complete the steps in [Prerequisites: Get ready to build an API in API Gateway](setting-up.md), including assigning API Gateway access permission to the IAM user\.

1.  At a minimum, follow the steps in [Tutorial: Build a REST API with HTTP non\-proxy integration](#api-gateway-create-api-step-by-step) to create a new API named `MyDemoAPI` in the API Gateway console\. 

### Step 1: Create resources<a name="getting-started-mappings-add-resources"></a>

In this step, you create three resources that enable the API to interact with the HTTP endpoint\.

**To create the first resource**

1.  In the **Resources** pane, select the resource root, as represented by a single forward slash \(`/`\), and then choose **Create Resource** from the **Actions** drop\-down menu\. 

1. For **Resource Name**, type **petstorewalkthrough**\.

1. For **Resource Path**, accept the default of **/petstorewalkthrough**, and then choose **Create Resource**\.

**To create the second resource**

1. In the **Resources** pane, choose **/petstorewalkthrough**, and then choose **Create Resource**\.

1. For **Resource Name**, type **pets**\.

1. For **Resource Path**, accept the default of **/petstorewalkthrough/pets**, and then choose **Create Resource**\. 

**To create the third resource**

1. In the **Resources** pane, choose **/petstorewalkthrough/pets**, and then choose **Create Resource**\.

1. For **Resource Name**, type **petId**\. This maps to the item ID in the HTTP endpoint\.

1. For **Resource Path**, overwrite **petid** with **\{petId\}**\. Use curly braces \(`{ }`\) around `petId` so that **/petstorewalkthrough/pets/\{petId\}** is displayed, and then choose **Create Resource**\.

   This maps to `/petstore/pets/my-item-id` in the HTTP endpoint\.

### Step 2: Create and test the methods<a name="getting-started-mappings-set-methods"></a>

 In this step, you integrate the methods with the backend HTTP endpoints, map the GET method request parameters to the corresponding integration request parameters, and then test the methods\. 

**To set up and test the first GET method**

 This procedure demonstrates the following: 
+ Create and integrate the method request of `GET /petstorewalkthrough/pets` with the integration request of `GET http://petstore-demo-endpoint.execute-api.com/petstore/pets`\.
+ Map the method request query parameters of `petType` and `petsPage` to the integration request query string parameters of `type` and `page`, respectively\.

1. In the **Resources** pane, choose **/petstorewalkthrough/pets**, choose **Create Method** from the **Actions** menu, and then choose **GET** under /**pets** from the drop\-down list of the method names\.

1. In the **/petstorewalkthrough/pets \- GET \- Setup** pane, choose **HTTP** for **Integration type** and choose **GET** for **HTTP method**\. 

1. For **Endpoint URL**, type **http://petstore\-demo\-endpoint\.execute\-api\.com/petstore/pets**\.

1. Choose **Save**\.

1. In the **Method Execution** pane, choose **Method Request**, and then choose the arrow next to **URL Query String Parameters**\.

1. Choose **Add query string**\.

1. For **Name**, type **petType**\.

   This specifies the `petType` query parameter in the API's method request\.

1. Choose the check mark icon to finish creating the method request URL query string parameter\.

1. Choose **Add query string** again\.

1. For **Name**, type **petsPage**\.

   This specifies the `petsPage` query parameter in the API's method request\.

1. Choose the check mark icon to finish creating the method request URL query string parameter\.

1. Choose **Method Execution**, choose **Integration Request**, and then choose the arrow next to **URL Query String Parameters**\.

1. Delete the **petType** entry mapped from **method\.request\.querystring\.petType** and the **petsPage** entry mapped from **method\.request\.querystring\.petsPage**\. You perform this step because the endpoint requires query string parameters named **type** and **page** for the request URL, instead of the default values\. 

1. Choose **Add query string**\.

1. For **Name**, type **type**\. This creates the required query string parameter for the integration request URL\.

1. For **Mapped from**, type **method\.request\.querystring\.petType**\. 

   This maps the method request's `petType` query parameter to the integration request's `type` query parameter\. 

1. Choose the check mark icon to finish creating the integration request URL query string parameter\. 

1. Choose **Add query string** again\.

1. For **Name**, type **page**\. This creates the required query string parameter for the integration request URL\.

1. For **Mapped from**, type **method\.request\.querystring\.petsPage**\.

   This maps the method request's `petsPage` query parameter to the integration request's `page` query parameter\. 

1. Choose the check mark icon to finish creating the integration request URL query string parameter\.

1. Choose **Method Execution**\. In the **Client** box, choose **TEST**\. In the **Query Strings** area, for **petType**, type **cat**\. For **petsPage**, type **2**\.

1. Choose **Test**\. If successful, **Response Body** displays the following:

   ```
   [
     {
       "id": 4,
       "type": "cat",
       "price": 999.99
     },
     {
       "id": 5,
       "type": "cat",
       "price": 249.99
     },
     {
       "id": 6,
       "type": "cat",
       "price": 49.97
     }
   ]
   ```

**To set up and test the second GET method**

 This procedure demonstrates the following: 
+ Create and integrate the method request of `GET /petstorewalkthrough/pets/{petId}` with the integration request of `GET http://petstore-demo-endpoint.execute-api.com/petstore/pets/{id}`\.
+ Map the method request path parameters of `petId` to the integration request path parameters of `id`\.

1. In the **Resources** list, choose **/petstorewalkthrough/pets/\{petId\}**, choose **Create Method** from the **Actions** drop\-down menu, and then choose **GET** as the HTTP verb for the method\.

1. In the **Setup** pane, choose **HTTP** for **Integration type** and choose **GET** for **HTTP method**\. 

1. For **Endpoint URL**, type **http://petstore\-demo\-endpoint\.execute\-api\.com/petstore/pets/\{id\}**\.

1. Choose **Save**\.

1. In the **Method Execution** pane, choose **Integration Request**, and then choose the arrow next to **URL Path Parameters**\.

1. Choose **Add path**\.

1. For **Name**, type **id**\.

1. For **Mapped from**, type **method\.request\.path\.petId**\.

   This maps the method request's path parameter of `petId` to the integration request's path parameter of `id`\.

1. Choose the check mark icon to finish creating the URL path parameter\.

1. Choose **Method Execution**, and in the **Client** box, choose **TEST**\. In the **Path** area, for **petId**, type **1**\.

1. Choose **Test**\. If successful, **Response Body** displays the following:

   ```
   {   
     "id": 1,
     "type": "dog",
     "price": 249.99
   }
   ```

**To set up and test the POST method**

 This procedure demonstrates the following: 
+ Create and integrate the method request of `POST /petstorewalkthrough/pets` with the integration request of `POST http://petstore-demo-endpoint.execute-api.com/petstore/pets`\.
+ Pass the method request JSON payload through to the integration request payload, without modification\.

1. In the **Resources** pane, choose **/petstorewalkthrough/pets**, choose **Create Method** from the **Actions** drop\-down menu, and then choose **POST** as the HTTP verb for the method\.

1. In the **Setup** pane, choose **HTTP** for **Integration type** and choose **POST** for **HTTP method**\. 

1. For **Endpoint URL**, type **http://petstore\-demo\-endpoint\.execute\-api\.com/petstore/pets**\.

1. Choose **Save**\.

1. In the **Method Execution** pane, in the **Client** box, choose **TEST**\. Expand **Request Body**, and then type the following:

   ```
   {
     "type": "dog",
     "price": 249.99
   }
   ```

1. Choose **Test**\. If successful, **Response Body** displays the following:

   ```
   {
     "pet": {
       "type": "dog",
       "price": 249.99
     },
     "message": "success"
   }
   ```

### Step 3: Deploy the API<a name="getting-started-mappings-deploy"></a>

In this step, you deploy the API so that you can begin calling it outside of the API Gateway console\.

**To deploy the API**

1. In the **Resources** pane, choose **Deploy API**\.

1. For **Deployment stage**, choose **`test`**\.
**Note**  
The input must be UTF\-8 encoded \(i\.e\., unlocalized\) text\.

1. For **Deployment description**, type **Calling HTTP endpoint walkthrough**\.

1. Choose **Deploy**\.

### Step 4: Test the API<a name="getting-started-mappings-test"></a>

In this step, you go outside of the API Gateway console and use your API to access the HTTP endpoint\.

1. In the **Stage Editor** pane, next to **Invoke URL**, copy the URL to the clipboard\. It should look something like this:

   ```
   https://my-api-id.execute-api.region-id.amazonaws.com/test
   ```

1. Paste this URL in the address box of a new browser tab\.

1. Append `/petstorewalkthrough/pets` so that it looks like this:

   ```
   https://my-api-id.execute-api.region-id.amazonaws.com/test/petstorewalkthrough/pets
   ```

   Browse to the URL\. The following information should be displayed:

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

1. After `petstorewalkthrough/pets`, type **?petType=cat&petsPage=2** so that it looks like this:

   ```
   https://my-api-id.execute-api.region-id.amazonaws.com/test/petstorewalkthrough/pets?petType=cat&petsPage=2
   ```

1. Browse to the URL\. The following information should be displayed:

   ```
   [
     {
       "id": 4,
       "type": "cat",
       "price": 999.99
     },
     {
       "id": 5,
       "type": "cat",
       "price": 249.99
     },
     {
       "id": 6,
       "type": "cat",
       "price": 49.97
     }
   ]
   ```

1. After `petstorewalkthrough/pets`, replace **?petType=cat&petsPage=2** with **/1** so that it looks like this:

   ```
   https://my-api-id.execute-api.region-id.amazonaws.com/test/petstorewalkthrough/pets/1
   ```

1. Browse to the URL\. The following information should be displayed:

   ```
   {
     "id": 1,
     "type": "dog",
     "price": 249.99
   }
   ```

1. Using a web debugging proxy tool or the cURL command\-line tool, send a POST method request to the URL from the previous procedure\. Append `/petstorewalkthrough/pets` so that it looks like this:

   ```
   https://my-api-id.execute-api.region-id.amazonaws.com/test/petstorewalkthrough/pets
   ```

   Append the following header:

   ```
   Content-Type: application/json
   ```

   Add the following code to the request body:

   ```
   {
     "type": "dog",
     "price": 249.99
   }
   ```

   For example, if you use the cURL command\-line tool, run a command similar to the following:

   ```
   curl -H "Content-Type: application/json" -X POST -d "{\"type\": \"dog\",\"price\": 249.99}" https://my-api-id.execute-api.region-id.amazonaws.com/test/petstorewalkthrough/pets
   ```

   The following information should be returned in the response body:

   ```
   {
     "pet": {
       "type": "dog",
       "price": 249.99
     },
     "message": "success"
   }
   ```

You have reached the end of this walkthrough\.

## Map response payload<a name="getting-started-models"></a>

In this walkthrough, we show how to use models and mapping templates in API Gateway to transform the output of an API call from one data schema to another\. This walkthrough builds on the instructions and concepts in the [Getting started with Amazon API Gateway](getting-started.md) and the [Map request parameters for an API Gateway API](#getting-started-mappings)\. If you have not yet completed those walkthroughs, we suggest you do them first\.

This walkthrough uses API Gateway to get example data from a publicly accessible HTTP endpoint and from an AWS Lambda function you create\. Both the HTTP endpoint and the Lambda function return the same example data:

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

You will use models and mapping templates to transform this data to one or more output formats\. In API Gateway, a model defines the format, also known as the schema or shape, of some data\. In API Gateway, a mapping template is used to transform some data from one format to another\. For more information, see [Working with models and mapping templates](models-mappings.md)\.

The first model and mapping template is used to rename `id` to `number`, `type` to `class`, and `price` to `salesPrice`, as follows:

```
[
  {
    "number": 1,
    "class": "dog",
    "salesPrice": 249.99
  },
  {
    "number": 2,
    "class": "cat",
    "salesPrice": 124.99
  },
  {
    "number": 3,
    "class": "fish",
    "salesPrice": 0.99
  }
]
```

The second model and mapping template is used to combine `id` and `type` into `description`, and to rename `price` to `askingPrice`, as follows:

```
[
  {
    "description": "Item 1 is a dog.",
    "askingPrice": 249.99
  },
  {
    "description": "Item 2 is a cat.",
    "askingPrice": 124.99
  },
  {
    "description": "Item 3 is a fish.",
    "askingPrice": 0.99
  }
]
```

The third model and mapping template is used to combine `id`, `type`, and `price` into a set of `listings`, as follows:

```
{
  "listings": [
    "Item 1 is a dog. The asking price is 249.99.",
    "Item 2 is a cat. The asking price is 124.99.",
    "Item 3 is a fish. The asking price is 0.99."
  ]
}
```

**Topics**
+ [Step 1: Create models](#getting-started-models-add-models)
+ [Step 2: Create resources](#getting-started-models-add-resources)
+ [Step 3: Create GET methods](#getting-started-models-add-methods)
+ [Step 4: Create a Lambda function](#getting-started-models-lambda)
+ [Step 5: Set up and test the methods](#getting-started-models-set-methods)
+ [Step 6: Deploy the API](#getting-started-models-deploy)
+ [Step 7: Test the API](#getting-started-models-test)
+ [Step 8: Clean up](#getting-started-models-clean-up)

### Step 1: Create models<a name="getting-started-models-add-models"></a>

In this step, you create four models\. The first three models represent the data output formats for use with the HTTP endpoint and the Lambda function\. The last model represents the data input schema for use with the Lambda function\.

**To create the first output model**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If **MyDemoAPI** is displayed, choose **Models**\.

1. Choose **Create**\.

1. For **Model name**, type **PetsModelNoFlatten**\.

1. For **Content type**, type **application/json**\.

1. For **Model description**, type **Changes id to number, type to class, and price to salesPrice**\.

1. For **Model schema**, type the following JSON Schema\-compatible definition:

   ```
   {
     "$schema": "http://json-schema.org/draft-04/schema#",
     "title": "PetsModelNoFlatten",
     "type": "array",
     "items": {
       "type": "object",
       "properties": {
         "number": { "type": "integer" },
         "class": { "type": "string" },
         "salesPrice": { "type": "number" }
       }
     }
   }
   ```

1. Choose **Create model**\.

**To create the second output model**

1. Choose **Create**\.

1. For **Model name**, type **PetsModelFlattenSome**\.

1. For **Content type**, type **application/json**\.

1. For **Model description**, type **Combines id and type into description, and changes price to askingPrice**\.

1. For **Model schema**, type the following:

   ```
   {
     "$schema": "http://json-schema.org/draft-04/schema#",
     "title": "PetsModelFlattenSome",
     "type": "array",
     "items": {
       "type": "object",
       "properties": {
         "description": { "type": "string" },
         "askingPrice": { "type": "number" }    
       }
     }
   }
   ```

1. Choose **Create model**\.

**To create the third output model**

1. Choose **Create**\.

1. For **Model name**, type **PetsModelFlattenAll**\.

1. For **Content type**, type **application/json**\.

1. For **Model description**, type **Combines id, type, and price into a set of listings**\.

1. For **Model schema**, type the following:

   ```
   {
     "$schema": "http://json-schema.org/draft-04/schema#",
     "title": "PetsModelFlattenAll",
     "type": "object",
     "properties": {
       "listings": {
         "type": "array",
         "items": {
           "type": "string"
         }
       }
     }
   }
   ```

1. Choose **Create model**\.

**To create the input model**

1. Choose **Create**\.

1. For **Model name**, type **PetsLambdaModel**\.

1. For **Content type**, type **application/json**\.

1. For **Model description**, type **GetPetsInfo model**\.

1. For **Model schema**, type the following:

   ```
   {
     "$schema": "http://json-schema.org/draft-04/schema#",
     "title": "PetsLambdaModel",
     "type": "array",
     "items": {
       "type": "object", 
       "properties": {
         "id": { "type": "integer" },
         "type": { "type": "string" },
         "price": { "type": "number" }
       }
     }
   }
   ```

1. Choose **Create model**\.

### Step 2: Create resources<a name="getting-started-models-add-resources"></a>

In this step, you create four resources\. The first three resources enable you to get the example data from the HTTP endpoint in the three output formats\. The last resource enables you to get the example data from the Lambda function in the output schema that combines `id` and `type` into `description` and renames `price` to `askingPrice`\.

**To create the first resource**

1. In the links list, choose **Resources**\.

1. In the **Resources** pane, choose **/petstorewalkthrough**, and then choose **Create Resource**\.

1. For **Resource Name**, type **NoFlatten**\.

1. For **Resource Path**, accept the default of **/petstorewalkthrough/noflatten**, and then choose **Create Resource**\.

**To create the second resource**

1. In the **Resources** pane, choose **/petstorewalkthrough** again, and then choose **Create Resource**\.

1. For **Resource Name**, type **FlattenSome**\.

1. For **Resource Path**, accept the default of **/petstorewalkthrough/flattensome**, and then choose **Create Resource**\.

**To create the third resource**

1. In the **Resources** pane, choose **/petstorewalkthrough** again, and then choose **Create Resource**\.

1. For **Resource Name**, type **FlattenAll**\.

1. For **Resource Path**, accept the default of **/petstorewalkthrough/flattenall**, and then choose **Create Resource**\.

**To create the fourth resource**

1. In the **Resources** pane, choose **/petstorewalkthrough** again, and then choose **Create Resource**\.

1. For **Resource Name**, type **LambdaFlattenSome**\.

1. For **Resource Path**, accept the default of **/petstorewalkthrough/lambdaflattensome**, and then choose **Create Resource**\.

### Step 3: Create GET methods<a name="getting-started-models-add-methods"></a>

In this step, you create a GET method for each of the resources you created in the previous step\.

**To create the first GET method**

1. In the **Resources** list, choose **/petstorewalkthrough/flattenall**, and then choose **Create Method**\.

1. From the drop\-down list, choose **GET**, and then choose the check mark icon to save your choice\.

1. In the Setup pane, choose **HTTP** for the **Integration type** and **GET** for **HTTP method**, type **http://petstore\-demo\-endpoint\.execute\-api\.com/petstore/pets** in **Endpoint URL**, and choose **Save**\.

**To create the second GET method**

1. In the **Resources** list, choose **/petstorewalkthrough/lambdaflattensome**, and then choose **Create Method**\.

1. From the drop\-down list, choose **GET**, and then choose the check mark to save your choice\.

1. In the Setup pane, choose **Lambda Function** for the **Integration type**, choose the region where you have created the [`GetPetsInfo` Lambda function](#getting-started-models-lambda) from the **Lambda Region** drop\-down list, choose **GetPetsInfo** for **Lambda Function**, and choose **Save**\. Choose **OK** when prompted to add permission to the Lambda function\.

**To create the third GET method**

1. In the **Resources** list, choose **/petstorewalkthrough/flattensome**, and then choose **Create Method**\.

1. From the drop\-down list, choose **GET**, and then choose the check mark icon to save your choice\.

1. In the Setup pane, choose **HTTP** for the **Integration type** and **GET** for **HTTP method**, type **http://petstore\-demo\-endpoint\.execute\-api\.com/petstore/pets** in **Endpoint URL**, and then choose **Save**\.

**To create the fourth GET method**

1. In the **Resources** list, choose **/petstorewalkthrough/noflatten**, and then choose **Actions**, **Create Method**\.

1. From the drop\-down list, choose **GET**, and then choose the check mark icon to save your choice\.

1. In the Setup pane, choose **HTTP** for the **Integration type** and **GET** for **HTTP method**, type **http://petstore\-demo\-endpoint\.execute\-api\.com/petstore/pets** in **Endpoint URL**, and then choose **Save**\.

### Step 4: Create a Lambda function<a name="getting-started-models-lambda"></a>

In this step, you create a Lambda function that returns the sample data\.

**To create the Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Do one of the following:
   + If a welcome page appears, choose **Get Started Now**\.
   + If the **Lambda: Function list** page appears, choose **Create a Lambda function**\.

1. For **Name**, type **GetPetsInfo**\.

1. For **Description**, type **Gets information about pets**\.

1. For **Code template**, choose **None**\.

1. Type the following code:

   ```
   console.log('Loading event');
   
   exports.handler = function(event, context, callback) {
     callback(null, 
       [{"id": 1, "type": "dog", "price": 249.99},
       {"id": 2, "type": "cat", "price": 124.99},
       {"id": 3, "type": "fish", "price": 0.99}]); // SUCCESS with message
   };
   ```
**Tip**  
In the preceding code, written in Node\.js, `console.log` writes information to an Amazon CloudWatch log\. `event` contains the input to the Lambda function\. `context` contains calling context\. `callback` returns the result\. For more information about how to write Lambda function code, see the "Programming Model" section in [AWS Lambda: How it Works](https://docs.aws.amazon.com/lambda/latest/dg/lambda-introduction.html) and the sample walkthroughs in the *AWS Lambda Developer Guide*\.

1. For **Handler name**, leave the default of `index.handler`\.

1. For **Role**, choose the Lambda execution role, **APIGatewayLambdaExecRole**, you created in the [Build an API Gateway REST API with Lambda integration](getting-started-with-lambda-integration.md)\.

1. Choose **Create Lambda function**\.

1. In the list of functions, choose **GetPetsInfo** to show the function's details\.

1. Make a note of the AWS region where you created this function\. You need it later\.

1. In the pop\-up list, choose **Edit or test function**\.

1. For **Sample event**, replace any code that appears with the following:

   ```
   {
                      
   }
   ```
**Tip**  
The empty curly braces mean that there are no input values for this Lambda function\. This function simply returns the JSON object containing the pets information, so those key\-value pairs are not required here\.

1. Choose **Invoke**\. **Execution result** shows `[{"id":1,"type":"dog","price":249.99},{"id":2,"type":"cat","price":124.99},{"id":3,"type":"fish","price":0.99}]`, which is also written to the CloudWatch Logs log files\.

1. Choose **Go to function list**\.

### Step 5: Set up and test the methods<a name="getting-started-models-set-methods"></a>

In this step, you configure the method responses, integration requests, and integration responses to specify the input and output data schemas \(or models\) for the GET methods associated with the HTTP endpoint and the Lambda function\. You also learn to test calling these methods using the API Gateway console\.

**To set up the integration for the first GET method and then test it**

1. From the API's **Resources** tree, choose **GET** under the **/petstorewalkthrough/flattenall** node\.

1. In the **Method Execution** pane, choose **Method Response**, and then choose the arrow next to **200**\. 

1. In the **Response Models for 200** area, for **application/json**, choose the pencil icon to start setting up the model for the method output\. For **Models**, choose **PetsModelFlattenAll**, and then choose the check mark icon to save the setting\.

1. Choose **Method Execution**, choose **Integration Response**, and then choose the arrow next to **200**\.

1. Expand the **Body Mapping Templates** section, choose **application/json** under **Content\-Type**\.

1.  For **Generate template from model**, choose **PetsModelFlattenAll** to display a mapping template after the `PetsModelFlattenAll` model as a starting point\.

1. Modify the mapping template code as follows:

   ```
   #set($inputRoot = $input.path('$'))
   {
     "listings" : [
   #foreach($elem in $inputRoot)
       "Item number $elem.id is a $elem.type. The asking price is $elem.price."#if($foreach.hasNext),#end
           
   #end
     ]
   }
   ```

1. Choose **Save**\.

1. Choose **Method Execution**, and in the **Client** box, choose **TEST**, and then choose **Test**\. If successful, **Response Body** displays the following:

   ```
   {
     "listings" : [
       "Item number 1 is a dog. The asking price is 249.99.",        
       "Item number 2 is a cat. The asking price is 124.99.",        
       "Item number 3 is a fish. The asking price is 0.99."        
     ]
   }
   ```

**To set up integration for the second GET method and then test it**

1. From the API's **Resources** tree, choose **GET** under the **/petstorewalkthrough/lambdaflattensome** node\.

1. In **Method Execution**, choose **Method Response**\. And then choose the arrow next to **200** to expand the section\. 

1. In the **Response Models for 200** area, choose the pencil icon on the row for the content type of **application/json**\. Choose **PetsModelFlattenSome** for **Models**, and then choose the check mark icon to save the choice\.

1. Go back to **Method Execution**\. Choose **Integration Response**, and then choose the arrow next to **200**\.

1. In the **Body Mapping Templates** section, choose **application/json** under **Content\-Type**\.

1. For **Generate template**, choose **PetsModelFlattenSome** to display the mapping script template for the output of this method\.

1. Modify the code as follows, and then choose **Save**:

   ```
   #set($inputRoot = $input.path('$'))
   [
   #foreach($elem in $inputRoot)
     {
       "description" : "Item $elem.id is a $elem.type.",
       "askingPrice" : $elem.price
     }#if($foreach.hasNext),#end
   
   #end
   ]
   ```

1. Choose **Method Execution**, and in the **Client** box, choose **TEST**, and then choose **Test**\. If successful, **Response Body** displays the following:

   ```
   [
     {
       "description" : "Item 1 is a dog.",
       "askingPrice" : 249.99
     },    
     {
       "description" : "Item 2 is a cat.",
       "askingPrice" : 124.99
     },    
     {
       "description" : "Item 3 is a fish.",
       "askingPrice" : 0.99
     }    
   ]
   ```

**To set up integration for the third GET method and then test it**

1. From the API's **Resources** tree, choose **GET** under the **/petstorewalkthrough/flattensome** node\.

1. In the **Method Execution** pane, choose **Method Response**\.

1. Choose the arrow next to **200**\. 

1. In the **Response Models for 200** area, for **application/json**, choose the pencil icon\. For **Models**, choose **PetsModelFlattenSome**, and then choose the check\-mark icon to save the choice\.

1. Go back to **Method Execution** and choose **Integration Response**\.

1. Choose the arrow next to **200** to expand the section\.

1. Expand the **Body Mapping Templates** area\. Choose **application/json** for **Content\-Type**\. For **Generate template**, choose **PetsModelFlattenSome** to display a mapping script template for the output of this method\.

1. Modify the code as follows:

   ```
   #set($inputRoot = $input.path('$'))
   [
   #foreach($elem in $inputRoot)
     {
       "description": "Item $elem.id is a $elem.type.",
       "askingPrice": $elem.price 
     }#if($foreach.hasNext),#end
   
   #end
   ]
   ```

1. Choose **Save**\.

1. Go back to **Method Execution** and choose **TEST** in the **Client** box\. And then choose **Test**\. If successful, **Response Body** displays the following:

   ```
   [
     {
       "description": "Item 1 is a dog.",
       "askingPrice": 249.99
     },    
     {
       "description": "Item 2 is a cat.",
       "askingPrice": 124.99
     },    
     {
       "description": "Item 3 is a fish.",
       "askingPrice": 0.99
     }    
   ]
   ```

**To set up integration for the fourth GET method and then test it**

1. From the API's **Resources** tree, choose **GET** under the **/petstorewalkthrough/noflatten** node\.

1. In the **Method Execution** pane, choose **Method Response**, and then expand the **200** section\. 

1. In the **Response Models for 200** area, for **application/json**, choose the pencil icon to update the response model for this method\. 

1. Choose **PetsModelNoFlatten** as the model for the content type of **application/json**, and then choose the check\-mark icon to save the choice\.

1. Choose **Method Execution**, choose **Integration Response**, and then choose the arrow next to **200** to expand the section\.

1. Expand the **Mapping Templates** section\. Choose **application/json** for **Content\-Type**\. For **Generate templates**, choose **PetsModelNoFlatten** to display a mapping script template for the output of this method\.

1. Modify the code as follows:

   ```
   #set($inputRoot = $input.path('$'))
   [
   #foreach($elem in $inputRoot)
     {
       "number": $elem.id,
       "class": "$elem.type",
       "salesPrice": $elem.price 
     }#if($foreach.hasNext),#end
   
   #end
   ]
   ```

1. Choose **Save**\.

1. Go back to **Method Execution**, and in the **Client** box, choose **TEST**, and then choose **Test**\. If successful, **Response Body** displays the following:

   ```
   [
     {
       "number": 1,
       "class": "dog",
       "salesPrice": 249.99
     },    
     {
       "number": 2,
       "class": "cat",
       "salesPrice": 124.99
     },    
     {
       "number": 3,
       "class": "fish",
       "salesPrice": 0.99
     }    
   ]
   ```

### Step 6: Deploy the API<a name="getting-started-models-deploy"></a>

In this step, you deploy the API so that you can begin calling it outside of the API Gateway console\.

**To deploy the API**

1. In the **Resources** pane, choose **Deploy API**\.

1. For **Deployment stage**, choose `test`\.

1. For **Deployment description**, type **Using models and mapping templates walkthrough**\.

1. Choose **Deploy**\.

### Step 7: Test the API<a name="getting-started-models-test"></a>

In this step, you go outside of the API Gateway console to interact with both the HTTP endpoint and the Lambda function\.

1. In the **Stage Editor** pane, next to **Invoke URL**, copy the URL to the clipboard\. It should look something like this:

   ```
   https://my-api-id.execute-api.region-id.amazonaws.com/test
   ```

1. Paste this URL in the address box of a new browser tab\.

1. Append `/petstorewalkthrough/noflatten` so that it looks like this:

   ```
   https://my-api-id.execute-api.region-id.amazonaws.com/test/petstorewalkthrough/noflatten
   ```

   Browse to the URL\. The following information should be displayed:

   ```
   [
     {
       "number": 1,
       "class": "dog",
       "salesPrice": 249.99
     },    
     {
       "number": 2,
       "class": "cat",
       "salesPrice": 124.99
     },    
     {
       "number": 3,
       "class": "fish",
       "salesPrice": 0.99
     }    
   ]
   ```

1. After `petstorewalkthrough/`, replace `noflatten` with `flattensome`\.

1. Browse to the URL\. The following information should be displayed:

   ```
   [
     {
       "description": "Item 1 is a dog.",
       "askingPrice": 249.99
     },    
     {
       "description": "Item 2 is a cat.",
       "askingPrice": 124.99
     },    
     {
       "description": "Item 3 is a fish.",
       "askingPrice": 0.99
     }    
   ]
   ```

1. After `petstorewalkthrough/`, replace `flattensome` with `flattenall`\.

1. Browse to the URL\. The following information should be displayed:

   ```
   {
     "listings" : [
       "Item number 1 is a dog. The asking price is 249.99.",        
       "Item number 2 is a cat. The asking price is 124.99.",        
       "Item number 3 is a fish. The asking price is 0.99."        
     ]
   }
   ```

1. After `petstorewalkthrough/`, replace `flattenall` with `lambdaflattensome`\.

1. Browse to the URL\. The following information should be displayed:

   ```
   [
     {
       "description" : "Item 1 is a dog.",
       "askingPrice" : 249.99
     },    
     {
       "description" : "Item 2 is a cat.",
       "askingPrice" : 124.99
     },    
     {
       "description" : "Item 3 is a fish.",
       "askingPrice" : 0.99
     }    
   ]
   ```

### Step 8: Clean up<a name="getting-started-models-clean-up"></a>

If you no longer need the Lambda function you created for this walkthrough, you can delete it now\. You can also delete the accompanying IAM resources\.

**Warning**  
If you delete a Lambda function your APIs rely on, those APIs will no longer work\. Deleting a Lambda function cannot be undone\. If you want to use the Lambda function again, you must re\-create the function\.  
If you delete an IAM resource a Lambda function relies on, the Lambda function and any APIs that rely on it will no longer work\. Deleting an IAM resource cannot be undone\. If you want to use the IAM resource again, you must re\-create the resource\. If you plan to continue experimenting with the resources you created for this and the other walkthroughs, do not delete the Lambda invocation role or the Lambda execution role\.

**To delete the Lambda function**

1. Sign in to the AWS Management Console and open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. On the **Lambda: Function list** page, in the list of functions, choose the button next to **GetPetsInfo**, and then choose **Actions**, **Delete**\. When prompted, choose **Delete** again\.

**To delete the associated IAM resources**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the **Details** area, choose **Roles**\.

1. Select **APIGatewayLambdaExecRole**, and then choose **Role Actions**, **Delete Role**\. When prompted, choose **Yes, Delete**\.

1. In the **Details** area, choose **Policies**\.

1. Select **APIGatewayLambdaExecPolicy**, and then choose **Policy Actions**, **Delete**\. When prompted, choose **Delete**\.

You have now reached the end of this walkthrough\.