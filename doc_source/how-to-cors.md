# Enable CORS for an API Gateway REST API Resource<a name="how-to-cors"></a>

[Cross\-origin resource sharing \(CORS\)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) is a browser security feature that restricts cross\-origin HTTP requests that are initiated from scripts running in the browser\. If your REST API's resources receive non\-simple cross\-origin HTTP requests, you need to enable CORS support\.

**Topics**
+ [Determining Whether to Enable CORS Support](#apigateway-cors-request-types)
+ [What It Means to Enable CORS Support](#apigateway-responding-to-cors-preflight)
+ [Testing CORS](#apigateway-test-cors)
+ [Enable CORS on a Resource Using the API Gateway Console](#how-to-cors-console)
+ [Enable CORS on a Resource Using the API Gateway Import API](enable-cors-for-resource-using-swagger-importer-tool.md)

## Determining Whether to Enable CORS Support<a name="apigateway-cors-request-types"></a>

A *cross\-origin* HTTP request is one that is made to:
+ A different *domain* \(for example, from `example.com` to `amazondomains.com`\)
+ A different *subdomain* \(for example, from `example.com` to `petstore.example.com`\)
+ A different *port* \(for example, from `example.com` to `example.com:10777`\)
+ A different *protocol* \(for example, from `https://example.com` to `http://example.com`\)

Cross\-origin HTTP requests can be divided into two types: *simple* requests and *non\-simple* requests\.

An HTTP request is *simple* if all of the following conditions are true:
+ It is issued against an API resource that allows only `GET`, `HEAD`, and `POST` requests\.
+ If it is a `POST` method request, it must include an `Origin` header\.
+ The request payload content type is `text/plain`, `multipart/form-data`, or `application/x-www-form-urlencoded`\.
+ The request does not contain custom headers\.
+ Any additional requirements that are listed in the [Mozilla CORS documentation for simple requests](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#Simple_requests)\.

For simple cross\-origin `POST` method requests, the response from your resource needs to include the header `Access-Control-Allow-Origin`, where the value of the header key is set to `'*'`\(any origin\) or is set to the origins allowed to access that resource\.

All other cross\-origin HTTP requests are *non\-simple* requests\. If your API's resources receive non\-simple requests, you'll need to enable CORS support\.

## What It Means to Enable CORS Support<a name="apigateway-responding-to-cors-preflight"></a>

When a browser receives a non\-simple HTTP request, the [CORS protocol](https://fetch.spec.whatwg.org/#http-cors-protocol) requires the browser to send a preflight request to the server and wait for approval \(or a request for credentials\) from the server before sending the actual request\. The *preflight request* appears to your API as an HTTP request that:
+ Includes an `Origin` header\.
+ Uses the `OPTIONS` method\.
+ Includes the following headers:
  + `Access-Control-Request-Method`
  + `Access-Control-Request-Headers`

To support CORS, therefore, a REST API resource needs to implement an `OPTIONS` method that can respond to the `OPTIONS` preflight request with at least the following response headers mandated by the Fetch standard:
+ `Access-Control-Allow-Methods`
+ `Access-Control-Allow-Headers`
+ `Access-Control-Allow-Origin`

How you enable CORS support depends on your API's integration type\.

### Enabling CORS Support for Mock Integrations<a name="apigateway-enable-cors-mock"></a>

For a mock integration, you enable CORS by creating an `OPTIONS` method to return the required response headers \(with appropriate static values\) as the method response headers\. In addition, each of the actual CORS\-enabled methods must also return the `Access-Control-Allow-Origin:'request-originating server addresses'` header in at least its 200 response, where the value of the header key is set to `'*'` \(any origin\) or is set to the origins allowed to access the resource\.

### Enabling CORS Support for Lambda or HTTP Non\-Proxy Integrations and AWS Service Integrations<a name="apigateway-enable-cors-nonproxy"></a>

For a Lambda custom \(non\-proxy\) integration, HTTP custom \(non\-proxy\) integration, or AWS service integration, you can set up the required headers by using API Gateway method response and integration response settings\. API Gateway will create an `OPTIONS` method and attempt to add the `Access-Control-Allow-Origin` header to your existing method integration responses\. This doesn’t always work and sometimes you need to manually modify the integration response to properly enable CORS\. Usually this just means manually modifying the integration response to return the `Access-Control-Allow-Origin` header\.

### Enabling CORS Support for Lambda or HTTP Proxy Integrations<a name="apigateway-enable-cors-proxy"></a>

For a Lambda proxy integration or HTTP proxy integration, you can still set up the required `OPTIONS` response headers in API Gateway\. However, your backend is responsible for returning the `Access-Control-Allow-Origin` and `Access-Control-Allow-Headers` headers, because a proxy integration doesn't return an integration response\.

The following is an example of a Node\.js Lambda function that is configured to return the required CORS headers:

```
'use strict';

exports.handler = function(event, context) {
    
    var responseCode = 200;
    
    var response = {
        statusCode: responseCode,
        headers: {
            "x-custom-header" : "my custom header value",
            "Access-Control-Allow-Origin": "my-origin.com"
        },
        body: JSON.stringify(event)
    };
    
    context.succeed(response);
};
```

A more complete Node\.js example can be found at [https://github\.com/awslabs/serverless\-application\-model/blob/master/examples/2016\-10\-31/api\_swagger\_cors/index\.js](https://github.com/awslabs/serverless-application-model/blob/master/examples/2016-10-31/api_swagger_cors/index.js)\.

The following is an example of a Python code snippet that returns the required CORS headers:

```
response["headers"] = {
    'Content-Type': 'application/json', 
    'Access-Control-Allow-Origin': '*' 
}
```

The following is an example that returns the required headers for CORS using Serverless Application Model \(SAM\), including `AllowHeaders`:

```
Globals:
  Api:
    # Allows an application running locally on port 8080 to call this API
    Cors:
      AllowMethods: "'OPTIONS,POST,GET'"
      AllowHeaders: "'Content-Type'"
      AllowOrigin: "'http://localhost:8080'"
```

The following is a Lambda proxy example that returns the same headers as the SAM example:

```
return {
    'statusCode': 200,
    'headers': {
        "Access-Control-Allow-Origin": "http://localhost:8080",
        "Access-Control-Allow-Headers": "Content-Type",
        "Access-Control-Allow-Methods": "OPTIONS,POST,GET"
    },
    'body': json.dumps(response)
}
```

## Testing CORS<a name="apigateway-test-cors"></a>

You can test CORS by customizing the following `cURL` command for the actual method and origin header you are using:

```
curl -v -X OPTIONS -H "Access-Control-Request-Method: POST" -H "Origin: http://example.com" https://{restapi_id}.execute-api.{region}.amazonaws.com/{stage_name}
```

## Enable CORS on a Resource Using the API Gateway Console<a name="how-to-cors-console"></a>

You can use the API Gateway console to enable CORS support for one or all methods on a REST API resource that you have created\.

**Important**  
Resources can contain child resources\. Enabling CORS support for a resource and its methods does not recursively enable it for child resources and their methods\.

**Enable CORS support on a REST API resource**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose the API from the **APIs** list\.

1. Choose a resource under **Resources**\. This will enable CORS for all the methods on the resource\.

   Alternatively, you could choose a method under the resource to enable CORS for just this method\.

1. Choose **Enable CORS** from the **Actions** drop\-down menu\.  
![\[Choose Enable CORS\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/amazon-api-gateway-enable-cors.png)

1.  In the **Enable CORS** form, do the following: 

   1.  In the **Access\-Control\-Allow\-Headers** input field, type a static string of a comma\-separated list of headers that the client must submit in the actual request of the resource\. Use the console\-provided header list of `'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'` or specify your own headers\. 

   1.  Use the console\-provided value of `'*'` as the **Access\-Control\-Allow\-Origin** header value to allow access requests from all origins, or specify origins to be permitted to access the resource\. 

   1. Choose **Enable CORS and replace existing CORS headers**\.  
![\[Choose which headers are allowed\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/amazon-api-gateway-enable-cors-resources.png)
**Important**  
 When applying the above instructions to the `ANY` method in a proxy integration, any applicable CORS headers will not be set\. Instead, your backend must return the applicable CORS headers, such as `Access-Control-Allow-Origin`\. 

1. In **Confirm method changes**, choose **Yes, overwrite existing values** to confirm the new CORS settings\.  
![\[Confirm overwrite of existing values\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/amazon-api-gateway-enable-cors-confirm-method-overwrite.png)

After CORS is enabled on the `GET` method, an `OPTIONS` method is added to the resource, if it is not already there\. The `200` response of the `OPTIONS` method is automatically configured to return the three `Access-Control-Allow-*` headers to fulfill preflight handshakes\. In addition, the actual \(`GET`\) method is also configured by default to return the `Access-Control-Allow-Origin` header in its 200 response as well\. For other types of responses, you will need to manually configure them to return `Access-Control-Allow-Origin'` header with '\*' or specific origins, if you do not want to return the `Cross-origin access` error\.

After you enable CORS support on your resource, you must deploy or redeploy the API for the new settings to take effect\. For more information, see [Deploy a REST API from the API Gateway Console](how-to-deploy-api-with-console.md)\.