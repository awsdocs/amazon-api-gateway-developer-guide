# Enabling CORS for a REST API resource<a name="how-to-cors"></a>

[Cross\-origin resource sharing \(CORS\)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) is a browser security feature that restricts cross\-origin HTTP requests that are initiated from scripts running in the browser\. If your REST API's resources receive non\-simple cross\-origin HTTP requests, you need to enable CORS support\.

## Determining whether to enable CORS support<a name="apigateway-cors-request-types"></a>

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

All other cross\-origin HTTP requests are *non\-simple* requests\. If your API's resources receive non\-simple requests, you need to enable CORS support\.

## What it means to enable CORS support<a name="apigateway-responding-to-cors-preflight"></a>

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

### Enabling CORS support for mock integrations<a name="apigateway-enable-cors-mock"></a>

For a mock integration, you enable CORS by creating an `OPTIONS` method to return the required response headers \(with appropriate static values\) as the method response headers\. In addition, each of the actual CORS\-enabled methods must also return the `Access-Control-Allow-Origin:'request-originating server addresses'` header in at least its 200 response, where the value of the header key is set to `'*'` \(any origin\) or is set to the origins allowed to access the resource\.

### Enabling CORS support for Lambda or HTTP non\-proxy integrations and AWS service integrations<a name="apigateway-enable-cors-nonproxy"></a>

For a Lambda custom \(non\-proxy\) integration, HTTP custom \(non\-proxy\) integration, or AWS service integration, you can set up the required headers by using API Gateway method response and integration response settings\. When you enable CORS by using the AWS Management Console, API Gateway creates an `OPTIONS` method and attempts to add the `Access-Control-Allow-Origin` header to your existing method integration responses\. This doesn’t always work, and sometimes you need to manually modify the integration response to properly enable CORS\. Usually this just means manually modifying the integration response to return the `Access-Control-Allow-Origin` header\.

### Enabling CORS support for Lambda or HTTP proxy integrations<a name="apigateway-enable-cors-proxy"></a>

For a Lambda proxy integration or HTTP proxy integration, you can still set up the required `OPTIONS` response headers in API Gateway\. However, your backend is responsible for returning the `Access-Control-Allow-Origin` and `Access-Control-Allow-Headers` headers, because a proxy integration doesn't return an integration response\.

The following example Lambda functions return the required CORS headers:

------
#### [ Node\.js ]

```
exports.handler = async (event) => {
    const response = {
        statusCode: 200,
        headers: {
            "Access-Control-Allow-Headers" : "Content-Type",
            "Access-Control-Allow-Origin": "https://www.example.com",
            "Access-Control-Allow-Methods": "OPTIONS,POST,GET"
        },
        body: JSON.stringify('Hello from Lambda!'),
    };
    return response;
};
```

------
#### [ Python 3 ]

```
import json

def lambda_handler(event, context):
    return {
        'statusCode': 200,
        'headers': {
            'Access-Control-Allow-Headers': 'Content-Type',
            'Access-Control-Allow-Origin': 'https://www.example.com',
            'Access-Control-Allow-Methods': 'OPTIONS,POST,GET'
        },
        'body': json.dumps('Hello from Lambda!')
    };
```

------

For a Node\.js example that uses the AWS Serverless Application Model, see [GitHub](https://github.com/awslabs/serverless-application-model/blob/master/examples/2016-10-31/api_swagger_cors/index.js)\.

**Topics**
+ [Determining whether to enable CORS support](#apigateway-cors-request-types)
+ [What it means to enable CORS support](#apigateway-responding-to-cors-preflight)
+ [Enable CORS on a resource using the API Gateway console](how-to-cors-console.md)
+ [Enable CORS on a resource using the API Gateway import API](enable-cors-for-resource-using-swagger-importer-tool.md)
+ [Testing CORS](apigateway-test-cors.md)