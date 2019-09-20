# Enable Mock Integration Using the API Gateway Console<a name="how-to-mock-integration-console"></a>

You must have the method available in API Gateway\. Follow the instructions in [TUTORIAL: Build an API with HTTP Non\-Proxy Integration](api-gateway-create-api-step-by-step.md)\.

1. Choose an API resource and create a method\. In the method **Setup** pane, choose **Mock** for **Integration type**, and then choose **Save**\.

1.  Choose **Method Request** from **Method Execution**\. Expand **URL Query String Parameters**\. Choose **Add query string** to add a `scope` query parameter\. This determines if the caller is internal or otherwise\.

1.  Choose **Integration Request** from **Method Execution**\. Expand **Body Mapping Templates**\. Choose or add an **application/json** mapping template\. Type the following in the template editor:

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

1. Choose **Integration Response** from **Method Execution**\. Expand the **200** response and then the **Body Mapping Templates** section\. Choose or add an application/json mapping template and type the following response body mapping template in the template editor\.

   ```
   {
       "statusCode": 200,
       "message": "Go ahead without me"
   }
   ```

   Choose **Save**\.

1.  Scroll to **Integration Response**\. Choose **Add integration response** to add a 500 response\. Type `5\d{2}` in **HTTP status regex**\. Expand **Body Mapping Templates** and choose **Add mapping template**\. Type `application/json` for **Content\-Type** and then choose the check mark icon to save the setting\. In the template editor, type the following integration response body mapping template:

   ```
   {
       "statusCode": 500,
       "message": "The invoked method is not supported on the API resource."
   }
   ```

   Choose **Save**\.

1.  Choose **Test** from **Method Execution**\. Do the following:

   1.  Type `internal` under **scope**\. Choose `Test`\. The test result shows:

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

   1.  Type `public` under `scope` or leave it blank\. Choose **Test**\. The test result shows:

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