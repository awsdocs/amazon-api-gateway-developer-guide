# Invoking a REST API in Amazon API Gateway<a name="how-to-call-api"></a>

To call a deployed API, clients submit requests to the URL for the API Gateway component service for API execution, known as `execute-api`\.

The base URL for REST APIs is in the following format: 

```
https://{restapi_id}.execute-api.{region}.amazonaws.com/{stage_name}/
```

where *\{restapi\_id\}* is the API identifier, *\{region\}* is the Region, and *\{stage\_name\}* is the stage name of the API deployment\. 

**Important**  
Before you can invoke an API, you must deploy it in API Gateway\. To do that, follow the instructions in [Deploying a REST API in Amazon API Gateway](how-to-deploy-api.md)\. 

**Topics**
+ [Obtain an API's invoke URL in the API Gateway console](#apigateway-how-to-call-rest-api)
+ [Use the API Gateway console to test a REST API method](how-to-test-method.md)
+ [Use Postman to call a REST API](how-to-use-postman-to-call-api.md)
+ [Call REST API through generated SDKs](how-to-call-api-using-generated-sdk.md)
+ [Call a REST API through AWS Amplify](how-to-call-api-using-aws-amplify-javascript-library.md)
+ [How to invoke a private API](apigateway-private-api-test-invoke-url.md)

## Obtain an API's invoke URL in the API Gateway console<a name="apigateway-how-to-call-rest-api"></a>

You can find a REST API's root URL in the **Stage Editor** for the API in the API Gateway console\. It's listed as the **Invoke URL** at the top\. If the API's root resource exposes a `GET` method without requiring user authentication, you can call the method by clicking the **Invoke URL** link\. You can also construct this root URL by combining the `host` and `basePath` fields of an exported OpenAPI definition file of the API\. 

If an API permits anonymous access, you can use any web browser to invoke any `GET` method calls by copying and pasting an appropriate invocation URL to the browser's address bar\. For other methods or any authentication\-required calls, the invocation are more involved because you must specify a payload or sign the requests\. You can handle these in a script behind an HTML page or in a client application using one of the AWS SDKs\.

For testing, you can use the API Gateway console to call an API by using the API Gateway's `TestInvoke` feature, which bypasses the `Invoke` URL and allows API testing before the API is deployed\. Alternatively, you can use the [Postman](http://www.postman.com/) application to test a successfully deployed API, without writing a script or a client\.

**Note**  
 Query string parameter values in an invocation URL cannot contain `%%`\. 