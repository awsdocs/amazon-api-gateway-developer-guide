# Set up a gateway response using the API Gateway console<a name="set-up-gateway-response-using-the-console"></a>

**To customize a gateway response using the API Gateway console**

1. Sign in to the API Gateway console\.

1. Choose your existing API or create a new one\.

1. Expand the API in the primary navigation pane and choose **Gateway Responses** under the API\. 

1. In the **Gateway Responses** pane, choose a response type\. In this walkthrough, we use **Missing Authentication Token \(403\)** as an example\. 

1. You can change the API Gateway\-generated **Status Code** to return a different status code that meets your API's requirements\. In this example, the customization changes the status code from the default \(`403`\) to `404` because this error message occurs when a client calls an unsupported or invalid resource that can be thought of as not found\.

1. To return custom headers, choose **Add Header** under **Response Headers**\. For illustration purposes, we add the following custom headers: 

   ```
   Access-Control-Allow-Origin:'a.b.c'
   x-request-id:method.request.header.x-amzn-RequestId
   x-request-path:method.request.path.petId
   x-request-query:method.request.querystring.q
   ```

   In the preceding header mappings, a static domain name \(`'a.b.c'`\) is mapped to the `Allow-Control-Allow-Origin` header to allow CORS access to the API; the input request header of `x-amzn-RequestId` is mapped to `request-id` in the response; the `petId` path variable of the incoming request is mapped to the `request-path` header in the response; and the `q` query parameter of the original request is mapped to the `request-query` header of the response\.

1. Under **Body Mapping Templates**, keep `application/json` for **Content Type** and enter the following body mapping template in the **Body Mapping Template** editor:

   ```
   {
        "message":"$context.error.messageString",
        "type": "$context.error.responseType",
        "statusCode": "'404'",
        "stage": "$context.stage",
        "resourcePath": "$context.resourcePath",
        "stageVariables.a": "$stageVariables.a"
   }
   ```

   This example shows how to map `$context` and `$stageVariables` properties to properties of the gateway response body\.

1. Choose **Save**\.

1. Deploy the API to a new or existing stage\.

1. Test it by calling the following CURL command, assuming the corresponding API method's invoke URL is `https://o81lxisefl.execute-api.us-east-1.amazonaws.com/custErr/pets/{petId}`:

   ```
   curl -v -H 'x-amzn-RequestId:123344566' https://o81lxisefl.execute-api.us-east-1.amazonaws.com/custErr/pets/5/type?q=1
   ```

   Because the extra query string parameter `q=1` isn't compatible with the API, an error is returned to trigger the specified gateway response\. You should get a gateway response similar to the following:

   ```
   > GET /custErr/pets/5?q=1 HTTP/1.1
   Host: o81lxisefl.execute-api.us-east-1.amazonaws.com
   User-Agent: curl/7.51.0
   Accept: */*
    
   HTTP/1.1 404 Not Found
   Content-Type: application/json
   Content-Length: 334
   Connection: keep-alive
   Date: Tue, 02 May 2017 03:15:47 GMT
   x-amzn-RequestId: 123344566
   Access-Control-Allow-Origin: a.b.c
   x-amzn-ErrorType: MissingAuthenticationTokenException
   header-1: static
   x-request-query: 1
   x-request-path: 5
   X-Cache: Error from cloudfront
   Via: 1.1 441811a054e8d055b893175754efd0c3.cloudfront.net (CloudFront)
   X-Amz-Cf-Id: nNDR-fX4csbRoAgtQJ16u0rTDz9FZWT-Mk93KgoxnfzDlTUh3flmzA==
    
   {
        "message":"Missing Authentication Token",
        "type": MISSING_AUTHENTICATION_TOKEN,
        "statusCode": '404',
        "stage": custErr,
        "resourcePath": /pets/{petId},
        "stageVariables.a": a
   }
   ```

   The preceding example assumes that the API backend is [Pet Store](http://petstore-demo-endpoint.execute-api.com/petstore/pets) and the API has a stage variable, `a`, defined\.