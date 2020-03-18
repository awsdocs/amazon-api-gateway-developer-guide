# Configuring CORS for an HTTP API<a name="http-api-cors"></a>

[Cross\-origin resource sharing \(CORS\)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) is a browser security feature that restricts HTTP requests that are initiated from scripts running in the browser\.

CORS is typically required to build web applications that access APIs hosted on a different domain or origin\. You can enable CORS to allow requests to your API from a web application hosted on a different domain\. For example, if your API is hosted on `https://{api_id}.execute-api.{region}.amazonaws.com/` and you want to call your API from a web application hosted on `example.com`, your API must support CORS\.

If you configure CORS for an API, API Gateway automatically sends a response to preflight OPTIONS requests, even if there isn't an OPTIONS route configured for your API\. For a CORS request, API Gateway adds the configured CORS headers to the response from an integration\.

**Note**  
If you configure CORS for an API, API Gateway ignores CORS headers returned from your backend integration\.

You can specify the following parameters in a CORS configuration\.


| CORS Headers | CORS Configuration Property | Example Values | 
| --- | --- | --- | 
|  Access\-Control\-Allow\-Origin  |  allowOrigins  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-cors.html)  | 
|  Access\-Control\-Allow\-Credentials  |  allowCredentials  |  true  | 
|  Access\-Control\-Expose\-Headers  |  exposeHeaders  |  Date, x\-api\-id  | 
|  Access\-Control\-Max\-Age  |  maxAge  |  300  | 
|  Access\-Control\-Allow\-Methods  |  allowMethods  |  GET, POST, DELETE, \*  | 
|  Access\-Control\-Allow\-Headers  |  allowHeaders  |  Authorization, \*  | 

## Configure CORS for an HTTP API by Using the AWS CLI<a name="http-api-cors.example"></a>

You can use the following command to enable CORS requests from `https://www.example.com`\.

**Example**  

```
aws apigatewayv2 update-api --api-id api-id --cors-configuration AllowOrigins="https://www.example.com"
```

For more information, see [CORS](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid.html#apis-apiid-model-cors) in the Amazon API Gateway Version 2 API Reference\.