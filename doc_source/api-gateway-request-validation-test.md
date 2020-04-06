# Test basic request validation in API Gateway<a name="api-gateway-request-validation-test"></a>

Choose one of the following topics for instructions on testing the basic request validation against the [sample API](api-gateway-request-validation-sample-api-swagger.md)\. 

**Topics**
+ [Test basic request validation using the API Gateway REST API](#api-gateway-request-validation-test-using-rest-api)
+ [Test basic request validation using the API Gateway console](#api-gateway-request-validation-test-in-console)

## Test basic request validation using the API Gateway REST API<a name="api-gateway-request-validation-test-using-rest-api"></a>

 To see the invocation URL of the deployed API, you can export the API from the stage, making sure to include the `Accept: application/json` or `Accept: application/yaml` header: 

```
GET /restapis/fjd6crafxc/stages/testStage/exports/swagger?extensions=validators HTTP/1.1
Accept: application/json
Content-Type: application/json
Host: apigateway.us-east-1.amazonaws.com
X-Amz-Date: 20170306T234936Z
Authorization: AWS4-HMAC-SHA256 Credential={access_key_ID}/20170306/us-east-1/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature={sig4_hash}
```

 You can ignore the `?extensions=validators` query parameter if you don't want to download the OpenAPI specifications related to the request validation\. 

**To test request validation using the API Gateway REST API calls**

1.  Call `GET /validation?q1=cat`\. 

   ```
   GET /testStage/validation?q1=cat HTTP/1.1
   Host: fjd6crafxc.execute-api.us-east-1.amazonaws.com
   Content-Type: application/json
   Accept: application/json
   ```

    Because the required parameter of `q1` is set and not blank, the request passes the validation\. API Gateway returns the following `200 OK` response: 

   ```
   [
       {
         "id": 1,
         "type": "cat",
         "price": 249.99
       },
       {
         "id": 2,
         "type": "cat",
         "price": 124.99
       },
       {
         "id": 3,
         "type": "cat",
         "price": 0.99
       }
   ]
   ```

1.  Call `GET /validation`\. 

   ```
   GET /testStage/validation HTTP/1.1
   Host: fjd6crafxc.beta.execute-api.us-east-1.amazonaws.com
   Content-Type: application/json
   Accept: application/json
   ```

    Because the required parameter of `q1` is not set, the request fails to pass the validation\. API Gateway returns the following `400 Bad Request` response: 

   ```
   {
       "message": "Missing required request parameters: [q1]"
   }
   ```

1.  Call `GET /validation?q1=`\. 

   ```
   GET /testStage/validation?q1= HTTP/1.1
   Host: fjd6crafxc.beta.execute-api.us-east-1.amazonaws.com
   Content-Type: application/json
   Accept: application/json
   ```

    Because the required parameter of `q1` is blank, the request fails to pass the validation\. API Gateway returns the same `400 Bad Request` response as in the previous example\. 

1.  Call `POST /validation`\. 

   ```
   POST /testStage/validation HTTP/1.1
   Host: fjd6crafxc.beta.execute-api.us-east-1.amazonaws.com
   Content-Type: application/json
   Accept: application/json
   h1: v1
   
   {
       "name" : "Marco",
       "type" : "dog",
       "price" : 260
   }
   ```

    Because the required header parameter of `h1` is set \(and not blank\) and the payload format adheres to the `RequestDataModel` required properties and associated constraints, the request passes the validation\. API Gateway returns the following successful response\. 

   ```
   {
       "pet": {
         "name": "Marco",
         "type": "dog",
         "price": 260
       },
       "message": "success"
   }
   ```

1.  Call `POST /validation`, without specifying the `h1` header or setting its value blank\. 

   ```
   POST /testStage/validation HTTP/1.1
   Host: fjd6crafxc.execute-api.us-east-1.amazonaws.com
   Content-Type: application/json
   Accept: application/json
   
   {
       "name" : "Marco",
       "type" : "dog",
       "price" : 260
   }
   ```

    Because the required header parameter of `h1` is missing or set to blank, the request fails to pass the validation\. API Gateway returns the following `400 Bad Request` response: 

   ```
   {
       "message": "Missing required request parameters: [h1]"
   }
   ```

1.  Call `POST /validation`, setting the `type` property of the payload to `bird`\. 

   ```
   POST /testStage/validation HTTP/1.1
   Host: fjd6crafxc.execute-api.us-east-1.amazonaws.com
   Content-Type: application/json
   Accept: application/json
   X-Amz-Date: 20170309T000215Z
   h1: v1
   
   {
       "name" : "Molly",
       "type" : "bird",
       "price" : 269
   }
   ```

    Because the `type` property value is not a member of the enumeration of `["dog", "cat", "fish"]`, the request fails to pass the validation\. API Gateway returns the following `400 Bad Request` response: 

   ```
   {
       "message": "Invalid request body"
   }
   ```

    Setting `price` to `501` violates the constraint on the property\. This causes the validation to fail and the same `400 Bad Request` response is returned\. 

## Test basic request validation using the API Gateway console<a name="api-gateway-request-validation-test-in-console"></a>

 The following steps describe how to test basic request validation in the API Gateway console\.

**To test the request validation on a method using TestInvoke in the API Gateway console**

While signed in to the API Gateway console, do the following:

1. Choose **Resources** for the API that you've configured a request validators map for\.

1. Choose a method for which you have enabled the request validation with a specified request validator\.

1. Under **Method Execution**, in the **Client** box, choose **Test**\.

1. Try different values for required request parameter or applicable body, and then choose **Test** to see the response\.

 When the method call passes validation, you should get expected responses\. If validation fails, the following error message returns if the payload is not the correct format: 

```
{
  "message": "Invalid request body"
}
```

If the request parameters aren't valid, the following error message returns:

```
{
  "message": "Missing required request parameters: [p1]"
}
```