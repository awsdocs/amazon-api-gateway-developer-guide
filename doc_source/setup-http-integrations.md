# Set up HTTP integrations in API Gateway<a name="setup-http-integrations"></a>

 You can integrate an API method with an HTTP endpoint using the HTTP proxy integration or the HTTP custom integration\. 

API Gateway supports the following endpoint ports: 80, 443 and 1024\-65535\.

 With proxy integration, setup is simple\. You only need to set the HTTP method and the HTTP endpoint URI, according to the backend requirements, if you are not concerned with content encoding or caching\. 

 With custom integration, setup is more involved\. In addition to the proxy integration setup steps, you need to specify how the incoming request data is mapped to the integration request and how the resulting integration response data is mapped to the method response\. 

**Topics**
+ [Set up HTTP proxy integrations in API Gateway](#api-gateway-set-up-http-proxy-integration-on-proxy-resource)
+ [Set up HTTP custom integrations in API Gateway](#set-up-http-custom-integrations)

## Set up HTTP proxy integrations in API Gateway<a name="api-gateway-set-up-http-proxy-integration-on-proxy-resource"></a>

To set up a proxy resource with the HTTP proxy integration type, create an API resource with a greedy path parameter \(for example, `/parent/{proxy+}`\) and integrate this resource with an HTTP backend endpoint \(for example, `https://petstore-demo-endpoint.execute-api.com/petstore/{proxy}`\) on the `ANY` method\. The greedy path parameter must be at the end of the resource path\. 

As with a non\-proxy resource, you can set up a proxy resource with the HTTP proxy integration by using the API Gateway console, importing an OpenAPI definition file, or calling the API Gateway REST API directly\. For detailed instructions about using the API Gateway console to configure a proxy resource with the HTTP integration, see [Tutorial: Build a REST API with HTTP proxy integration](api-gateway-create-api-as-simple-proxy-for-http.md)\.

The following OpenAPI definition file shows an example of an API with a proxy resource that is integrated with the [PetStore](http://petstore-demo-endpoint.execute-api.com/petstore/pets) website\.

------
#### [ OpenAPI 3\.0 ]

```
{
   "openapi": "3.0.0",
   "info": {
      "version": "2016-09-12T23:19:28Z",
      "title": "PetStoreWithProxyResource"
   },
   "paths": {
      "/{proxy+}": {
         "x-amazon-apigateway-any-method": {
            "parameters": [
               {
                  "name": "proxy",
                  "in": "path",
                  "required": true,
                  "schema": {
                     "type": "string"
                  }
               }
            ],
            "responses": {},
            "x-amazon-apigateway-integration": {
               "responses": {
                  "default": {
                     "statusCode": "200"
                  }
               },
               "requestParameters": {
                  "integration.request.path.proxy": "method.request.path.proxy"
               },
               "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/{proxy}",
               "passthroughBehavior": "when_no_match",
               "httpMethod": "ANY",
               "cacheNamespace": "rbftud",
               "cacheKeyParameters": [
                  "method.request.path.proxy"
               ],
               "type": "http_proxy"
            }
         }
      }
   },
   "servers": [
      {
         "url": "https://4z9giyi2c1.execute-api.us-east-1.amazonaws.com/{basePath}",
         "variables": {
            "basePath": {
              "default": "/test"
            }
         }
      }
   ]
}
```

------
#### [ OpenAPI 2\.0 ]

```
{
  "swagger": "2.0",
  "info": {
    "version": "2016-09-12T23:19:28Z",
    "title": "PetStoreWithProxyResource"
  },
  "host": "4z9giyi2c1.execute-api.us-east-1.amazonaws.com",
  "basePath": "/test",
  "schemes": [
    "https"
  ],
  "paths": {
    "/{proxy+}": {
      "x-amazon-apigateway-any-method": {
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "proxy",
            "in": "path",
            "required": true,
            "type": "string"
          }
        ],
        "responses": {},
        "x-amazon-apigateway-integration": {
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "requestParameters": {
            "integration.request.path.proxy": "method.request.path.proxy"
          },
          "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/{proxy}",
          "passthroughBehavior": "when_no_match",
          "httpMethod": "ANY",
          "cacheNamespace": "rbftud",
          "cacheKeyParameters": [
            "method.request.path.proxy"
          ],
          "type": "http_proxy"
        }
      }
    }
  }
}
```

------

In this example, a cache key is declared on the `method.request.path.proxy` path parameter of the proxy resource\. This is the default setting when you create the API using the API Gateway console\. The API's base path \(`/test`, corresponding to a stage\) is mapped to the website's PetStore page \(`/petstore`\)\. The single integration request mirrors the entire PetStore website using the API's greedy path variable and the catch\-all `ANY` method\. The following examples illustrate this mirroring\. 
+ **Set `ANY` as `GET` and `{proxy+}` as `pets`**

  Method request initiated from the frontend:

  ```
  GET https://4z9giyi2c1.execute-api.us-west-2.amazonaws.com/test/pets HTTP/1.1
  ```

  Integration request sent to the backend:

  ```
  GET http://petstore-demo-endpoint.execute-api.com/petstore/pets HTTP/1.1
  ```

  The run\-time instances of the `ANY` method and proxy resource are both valid\. The call returns a `200 OK` response with the payload containing the first batch of pets, as returned from the backend\.
+ **Set `ANY` as `GET` and `{proxy+}` as `pets?type=dog`**

  ```
  GET https://4z9giyi2c1.execute-api.us-west-2.amazonaws.com/test/pets?type=dog HTTP/1.1
  ```

  Integration request sent to the backend:

  ```
  GET http://petstore-demo-endpoint.execute-api.com/petstore/pets?type=dog HTTP/1.1
  ```

  The run\-time instances of the `ANY` method and proxy resource are both valid\. The call returns a `200 OK` response with the payload containing the first batch of specified dogs, as returned from the backend\.
+ **Set `ANY` as `GET` and `{proxy+}` as `pets/{petId}`**

  Method request initiated from the frontend:

  ```
  GET https://4z9giyi2c1.execute-api.us-west-2.amazonaws.com/test/pets/1 HTTP/1.1
  ```

  Integration request sent to the backend:

  ```
  GET http://petstore-demo-endpoint.execute-api.com/petstore/pets/1 HTTP/1.1
  ```

  The run\-time instances of the `ANY` method and proxy resource are both valid\. The call returns a `200 OK` response with the payload containing the specified pet, as returned from the backend\.
+ **Set `ANY` as `POST` and `{proxy+}` as `pets`**

  Method request initiated from the frontend:

  ```
  POST https://4z9giyi2c1.execute-api.us-west-2.amazonaws.com/test/pets HTTP/1.1
  Content-Type: application/json
  Content-Length: ...
  
  {
    "type" : "dog",
    "price" : 1001.00
  }
  ```

  Integration request sent to the backend:

  ```
  POST http://petstore-demo-endpoint.execute-api.com/petstore/pets HTTP/1.1
  Content-Type: application/json
  Content-Length: ...
  
  {
    "type" : "dog",
    "price" : 1001.00
  }
  ```

  The run\-time instances of the `ANY` method and proxy resource are both valid\. The call returns a `200 OK` response with the payload containing the newly created pet, as returned from the backend\.
+ **Set `ANY` as `GET` and `{proxy+}` as `pets/cat`**

  Method request initiated from the frontend:

  ```
  GET https://4z9giyi2c1.execute-api.us-west-2.amazonaws.com/test/pets/cat
  ```

  Integration request sent to the backend:

  ```
  GET http://petstore-demo-endpoint.execute-api.com/petstore/pets/cat
  ```

  The run\-time instance of the proxy resource path does not correspond to a backend endpoint and the resulting request is invalid\. As a result, a `400 Bad Request` response is returned with the following error message\. 

  ```
  {
    "errors": [
      {
        "key": "Pet2.type",
        "message": "Missing required field"
      },
      {
        "key": "Pet2.price",
        "message": "Missing required field"
      }
    ]
  }
  ```
+ **Set `ANY` as `GET` and `{proxy+}` as `null`**

  Method request initiated from the frontend:

  ```
  GET https://4z9giyi2c1.execute-api.us-west-2.amazonaws.com/test
  ```

  Integration request sent to the backend:

  ```
  GET http://petstore-demo-endpoint.execute-api.com/petstore/pets
  ```

  The targeted resource is the parent of the proxy resource, but the run\-time instance of the `ANY` method is not defined in the API on that resource\. As a result, this `GET` request returns a `403 Forbidden` response with the `Missing Authentication Token` error message as returned by API Gateway\. If the API exposes the `ANY` or `GET` method on the parent resource \(`/`\), the call returns a `404 Not Found` response with the `Cannot GET /petstore` message as returned from the backend\.

For any client request, if the targeted endpoint URL is invalid or the HTTP verb is valid but not supported, the backend returns a `404 Not Found` response\. For an unsupported HTTP method, a `403 Forbidden` response is returned\.

## Set up HTTP custom integrations in API Gateway<a name="set-up-http-custom-integrations"></a>

 With the HTTP custom integration, you have more control of which data to pass between an API method and an API integration and how to pass the data\. You do this using data mappings\. 

As part of the method request setup, you set the [requestParameters](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/#requestParameters) property on a [Method](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/) resource\. This declares which method request parameters, which are provisioned from the client, are to be mapped to integration request parameters or applicable body properties before being dispatched to the backend\. Then, as part of the integration request setup, you set the [requestParameters](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#requestParameters) property on the corresponding [Integration](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) resource to specify the parameter\-to\-parameter mappings\. You also set the [requestTemplates](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#requestTemplates) property to specify mapping templates, one for each supported content type\. The mapping templates map method request parameters, or body, to the integration request body\. 

 Similarly, as part of the method response setup, you set the [responseParameters](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/#responseParameters) property on the [MethodResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/) resource\. This declares which method response parameters, to be dispatched to the client, are to be mapped from integration response parameters or certain applicable body properties that were returned from the backend\. Then, as part of the integration response setup, you set the [responseParameters](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/#responseParameters) property on the corresponding [IntegrationResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/) resource to specify the parameter\-to\-parameter mappings\. You also set the [responseTemplates](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/#responseTemplates) map to specify mapping templates, one for each supported content type\. The mapping templates map integration response parameters, or integration response body properties, to the method response body\. 

 For more information about setting up mapping templates, see [Setting up data transformations for REST APIs](rest-api-data-transformations.md)\. 