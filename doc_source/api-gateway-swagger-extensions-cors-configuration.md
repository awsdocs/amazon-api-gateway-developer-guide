# x\-amazon\-apigateway\-cors object<a name="api-gateway-swagger-extensions-cors-configuration"></a>

Specifies the cross\-origin resource sharing \(CORS\) configuration for an HTTP API\. The extension applies to the root\-level OpenAPI structure\. To learn more, see [Configuring CORS for an HTTP API](http-api-cors.md)\.


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| allowOrigins | Array |  Specifies the allowed origins\.  | 
| allowCredentials | Boolean |  Specifies whether credentials are included in the CORS request\.  | 
| exposeHeaders | Array |  Specifies the headers that are exposed\.   | 
| maxAge | Integer |  Specifies the number of seconds that the browser should cache preflight request results\.  | 
| allowMethods | Array |  Specifies the allowed HTTP methods\.  | 
| allowHeaders | Array |  Specifies the allowed headers\.  | 

## x\-amazon\-apigateway\-cors example<a name="api-gateway-swagger-extensions-cors-configuration"></a>

The following is an example CORS configuration for an HTTP API\.

```
"x-amazon-apigateway-cors": {
    "allowOrigins": [
      "https://www.example.com"
    ],
    "allowCredentials": true,
    "exposeHeaders": [
      "x-apigateway-header",
      "x-amz-date",
      "content-type"
    ],
    "maxAge": 3600,
    "allowMethods": [
      "GET",
      "OPTIONS",
      "POST"
    ],
    "allowHeaders": [
      "x-apigateway-header",
      "x-amz-date",
      "content-type"
    ]
}
```