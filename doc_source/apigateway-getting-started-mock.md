# Create a REST API with a mock integration in Amazon API Gateway<a name="apigateway-getting-started-mock"></a>

You can use this walkthrough to create and deploy an API with a mock integration in Amazon API Gateway\. A *mock integration* enables your API to return a response for a request directly, without ever sending the request to a backend\. This enables you to develop your API independently from your backend\.

**Topics**
+ [Step 1: Create the API](#apigateway-getting-started-mock-step-1)
+ [Step 2: Create the mock integration](#apigateway-getting-started-mock-step-2)
+ [Step 3: Define the successful response](#apigateway-getting-started-mock-step-3)
+ [Step 4: Add an HTTP 500 status code and error message](#apigateway-getting-started-mock-step-4)
+ [Step 5: Test the mock integration](#apigateway-getting-started-mock-step-5)
+ [Next steps](#apigateway-getting-started-mock-next-steps)

## Step 1: Create the API<a name="apigateway-getting-started-mock-step-1"></a>

In this step, you create an API with a `GET` method and a query parameter in the API Gateway console\.

1. If you haven't already done so, complete the steps in [Prerequisites: Get ready to build an API in API Gateway](setting-up.md)\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If this is your first time using API Gateway, you see a page that introduces you to the features of the service\. Under **REST API**, choose **Build**\. When the **Create Example API** popup appears, choose **OK**\.

   If this is not your first time using API Gateway, choose **Create API**\. Under **REST API**, choose **Build**\.

1. Under **Create new API**, choose **New API**\.

1. Under **Settings**:
   + For **API name**, enter `my-api`\.
   + If desired, enter a description in the **Description** field; otherwise, leave it empty\.
   + Leave **Endpoint Type** set to **Regional**\.

1. Choose **Create API**\.

1. From the **Actions** dropdown menu, choose **Create Method**\.

1. Under the resource name \(**/**\), you'll see a dropdown menu\. Choose **GET** and then choose the checkmark icon to save your choice\.

1. You'll see a **/ – GET – Setup** pane\. For **Integration Type**, choose **Mock**\.  
![\[API Gateway method setup pane\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-mock-choose-integration.png)

1. Choose **Save**\. Now you'll see a **/ – GET – Method Execution** pane:  
![\[API Gateway method execution pane\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-mock-integration-boxes.png)

   The **Method Execution** pane contains these items, in clockwise order:
   + **Client**: This box represents the client \(browser or app\) that calls your API's `GET` method\. If you choose the **Test** link and then choose **Test**, this simulates a `GET` request from a client\.
   + **Method Request**: This box represents the client's `GET` request as it is received by API Gateway\. If you choose **Method Request**, you'll see settings for things like authorization and modifying the method request before it's passed through to the backend as an integration request\.
   + **Integration Request**: This box represents the `GET` request as would be passed to the backend\. For a mock integration, **Mapping Templates** can be used to specify the response \(HTTP status code and message\) to be returned\.
   + **Mock Endpoint**: This box represents the empty backend\.
   + **Integration Response**: This box represents the response from the backend, before it's passed to the client as a method response\. For a mock integration, the response is defined in the **Integration Request** box \(because there is no backend to supply it\)\. You can configure the integration response to transform an arbitrary output into a form that's appropriate for the client application\. You'll learn how to do that later in this walkthrough\.
   + **Method Response**: This box represents the HTTP status codes that can be returned to the client\. For a mock integration, the rest of the response is configured in the **Integration Response** box for each status code\.

   For this step, leave everything set to the default values\.

## Step 2: Create the mock integration<a name="apigateway-getting-started-mock-step-2"></a>

In this step, instead of integrating with a backend, you add a query string parameter to your `GET` method, and then you specify the response codes and messages your API will return\.

First, create the query string parameter:

1. In the **/ \- GET \- Method Execution** pane, choose **Method Request**\.

1. In the **/ \- GET \- Method Request** pane, expand **URL Query String Parameters**\.

1. Choose **Add query string**\.

1. Type `myParam` for the **Name** and choose the checkmark icon to save your choice\.

Next, create a mapping template that maps the query string parameter values to the HTTP status code values to be returned to the client\.

1. Choose **Method Execution**\.

1. Choose **Integration Request**\.

1. Expand **Mapping Templates**\.

1. For **Request body passthrough**, choose **When there are no templates defined \(recommended\)**\.

1. Under **Content\-Type**, choose **application/json**\.

1. Replace the template contents with the following:

   ```
   {
     #if( $input.params('myParam') == "myValue" )
       "statusCode": 200
     #else
       "statusCode": 500
     #end
   }
   ```

1. Choose **Save**\.

## Step 3: Define the successful response<a name="apigateway-getting-started-mock-step-3"></a>

Now you'll create a mapping template that maps the HTTP 200 status code value to a success message to be returned to the client\.

1. Choose **Method Execution** and **Integration Response**\.

1. Expand the **200** response and then expand the **Mapping Templates** section\.

1. Under **Content\-Type**, choose **application/json**\.

1. For the template contents, enter the following:

   ```
   {
       "statusCode": 200,
       "message": "Hello from API Gateway!"
   }
   ```

1. Choose **Save**\.
**Note**  
There are two **Save** buttons in this pane\. Be sure to choose the one in the **Mapping Templates** section\.

## Step 4: Add an HTTP 500 status code and error message<a name="apigateway-getting-started-mock-step-4"></a>

In this step you add an HTTP 500 status code that the frontend can return to the client, and then you map it to an error message\.

First, add the 500 status code:

1. Choose **Method Execution** and **Method Response**\.

1. Choose **Add response**\.

1. For **HTTP status**, enter `500` and choose the checkmark icon to save the setting\.

Now create a mapping template that maps the "returned" 500 status code value to an error message from the frontend to the client:

1. Choose **Method Execution** and **Integration Response**\.

1. Choose **Add integration response**\.

1. For **HTTP status regex**, enter `5\d{2}`\.

1. For **Method response status**, choose **500** from the dropdown menu and choose **Save**\.

1. Expand the **500** response and then expand the **Mapping Templates** section, if it isn't already expanded\.

1. Under **Content\-Type**, choose **Add mapping template**\.

1. In the box under **Content\-Type**, enter `application/json` and choose the checkmark icon to save your choice\.

1. For the template contents, enter the following:

   ```
   {
       "statusCode": 500,
       "message": "This is an error message."
   }
   ```

1. Choose **Save**\.
**Note**  
There are two **Save** buttons in this pane\. Be sure to choose the one in the **Mapping Templates** section\.

## Step 5: Test the mock integration<a name="apigateway-getting-started-mock-step-5"></a>

In this step, you test the mock integration\.

1. Choose **Method Execution** and then choose **Test**\.

1. Under **Query Strings**, enter `myParam=myValue`\.

1. Choose **Test**\.

   You should see output like the following:  
![\[Test 200 mock value\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-mock-test-200.png)

1. Under **Query Strings**, enter `myParam=""`\. Or simply delete everything in the **Query Strings** box\.

1. Choose **Test**\.

   You should see output like the following:  
![\[Test 500 mock value\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-mock-test-500.png)

## Next steps<a name="apigateway-getting-started-mock-next-steps"></a>

Explore any or all of the following topics to continue getting familiar with Amazon API Gateway\.


| To learn more about | Go to | 
| --- | --- | 
| Defining method requests and responses | [Set up REST API methods in API Gateway](how-to-method-settings.md) | 
| Defining integration responses | [Set up an integration response in API Gateway](api-gateway-integration-settings-integration-response.md) | 
| Setting up a custom domain name for your API | [Setting up custom domain names for REST APIs](how-to-custom-domains.md) | 
| Enabling CORS for your API | [Enabling CORS for a REST API resource](how-to-cors.md) | 

To get help with Amazon API Gateway from the community, see the [API Gateway Discussion Forum](http://forums.aws.amazon.com/forum.jspa?forumID=199)\. \(When you enter this forum, AWS might require you to sign in\.\)

To get help with API Gateway directly from AWS, see the support options on the [AWS Support page](http://aws.amazon.com/premiumsupport/)\.

See also our [frequently asked questions \(FAQs\)](http://aws.amazon.com/api-gateway/faqs/), or [contact us directly](http://aws.amazon.com/contact-us/)\.