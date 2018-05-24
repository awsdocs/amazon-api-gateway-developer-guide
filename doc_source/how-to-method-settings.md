# Set up API Methods in API Gateway<a name="how-to-method-settings"></a>

 In API Gateway, an API method embodies a [method request](http://docs.aws.amazon.com/apigateway/api-reference/resource/method/) and a [method response](http://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/)\. You set up an API method to define what a client should or must do to submit a request to access the service at the backend and to define the responses that the client receives in return\. For input, you can choose method request parameters, or an applicable payload, for the client to provide the required or optional data at run time\. For output, you determine the method response status code, headers, and applicable body as targets to map the backend response data into, before they are returned to the client\. To help the client developer understand the behaviors and the input and output formats of your API, you can [document your API](api-gateway-documenting-api.md) and [provide proper error messages](customize-gateway-responses.md) for [invalid requests](api-gateway-method-request-validation.md)\. 

An API method request is an HTTP request\. To set up the method request, you configure an HTTP method \(or verb\), the path to an API [resource](http://docs.aws.amazon.com/apigateway/api-reference/resource/resource/), headers, applicable query string parameters\. You also configure a payload when the HTTP method is `POST`, `PUT`, or `PATCH`\. For example, to retrieve a pet using the [PetStore sample API](api-gateway-create-api-from-example.md), you define the API method request of `GET /pets/{petId}`, where `{petId}` is a path parameter that can take a number at run time\.

```
GET /pets/1
Host: apigateway.us-east-1.amazonaws.com
...
```

 If the client specifies an incorrect path, for example, `/pet/1` or `/pets/one` instead of `/pets/1`, an exception is thrown\. 

An API method response is an HTTP response of a given status code\. For a non\-proxy integration, you must set up method responses to specify the required or optional targets of mappings\. These transform integration response headers or body to associated method response headers or body\. The mapping can be an identical transformation that passes through the integration as\-is\. For example, the following `200` method response shows an example of pass\-through of a successful integration response as\-is\. 

```
200 OK 
Content-Type: application/json
...

{
    "id": "1",
    "type": "dog",
    "price": "$249.99"
}
```

In principle, you can define a method response corresponding to a specific response from the backend\. Typically, this involves any 2XX, 4XX, and 5XX responses\. However, this may not be practical because often you may not know in advance all the responses that a backend may return\. In practice, you can designate one method response as the default to handle the unknown or unmapped responses from the backend\. It is a good practice to designate the 500 response as the default\. In any case, you must set up at least one method response for non\-proxy integrations\. Otherwise, API Gateway returns a 500 error response to the client even when the request succeeds at the backend\.

 To support a strongly typed SDK, such as a Java SDK, for your API, you should define the data model for input for the method request, and define the data model for output of the method response\. 

**Topics**
+ [Set up a Method Request in API Gateway](api-gateway-method-settings-method-request.md)
+ [Set up Method Responses in API Gateway](api-gateway-method-settings-method-response.md)
+ [Set up a Method Using the API Gateway Console](how-to-set-up-method-using-console.md)