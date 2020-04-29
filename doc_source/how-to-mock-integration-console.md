# Enable mock integration using the API Gateway console<a name="how-to-mock-integration-console"></a>

You must have the method available in API Gateway\. Follow the instructions in [Tutorial: Build a REST API with HTTP non\-proxy integration](api-gateway-create-api-step-by-step.md)\.

1. Choose an API resource and create a method\. In the method **Setup** pane, choose **Mock** for **Integration type**, and then choose **Save**\.

1.  Choose **Method Request** from **Method Execution**\. Expand **URL Query String Parameters**\. Choose **Add query string** to add a `scope` query parameter\. This determines if the caller is internal or otherwise\.

1. From **Method Execution**, choose **Method Response**\.

1. Under **HTTP Status**, add **500**\.

1. Choose **Integration Request** from **Method Execution**\. Expand **Mapping Templates**\. Choose or add an **application/json** mapping template\. Enter the following in the template editor:

   ```
   {
     #if( $input.params('scope') == "internal" )
       "statusCode": 200
     #else
       "statusCode": 500
     #end
   }
   ```

   Choose **Save**\.

1. Choose **Integration Response** from **Method Execution**\. Expand the **200** response and then the **Mapping Templates** section\. Choose or add an application/json mapping template and enter the following response body mapping template in the template editor\.

   ```
   {
       "statusCode": 200,
       "message": "Go ahead without me"
   }
   ```

   Choose **Save**\.

1. Choose **Add integration response** to add a 500 response\. Enter `5\d{2}` in **HTTP status regex**\. For **Method response status**, choose **500**, and then choose **Save**\.

1. Expand **5\\d\{2\}** and then expand **Mapping Templates** and choose **Add mapping template**\. Enter `application/json` for **Content\-Type** and then choose the check mark icon to save the setting\. In the template editor, enter the following integration response body mapping template:

   ```
   {
       "statusCode": 500,
       "message": "The invoked method is not supported on the API resource."
   }
   ```

   Choose **Save**\.

1.  Choose **Test** from **Method Execution**\. Do the following:

   1. Enter `scope=internal` under **Query Strings**\. Choose `Test`\. The test result shows:

      ```
      Request: /?scope=internal
      Status: 200
      Latency: 26 ms
      Response Body
      
      {
        "statusCode": 200,
        "message": "Go ahead without me"
      }
      
      Response Headers
      
      {"Content-Type":"application/json"}
      ```

   1. Enter `scope=public` under `Query Strings` or leave it blank\. Choose **Test**\. The test result shows:

      ```
      Request: /
      Status: 500
      Latency: 16 ms
      Response Body
      
      {
        "statusCode": 500,
        "message": "The invoked method is not supported on the API resource."
      }
      
      Response Headers
      
      {"Content-Type":"application/json"}
      ```

You can also return headers in a mock integration response by first adding a header to the method response and then setting up a header mapping in the integration response\. In fact, this is how the API Gateway console enables CORS support by returning CORS required headers\.