# x\-amazon\-apigateway\-auth object<a name="api-gateway-swagger-extensions-auth"></a>

Defines an authentication type to be applied for authentication of method invocations in API Gateway\.


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| type | string | Specifies the authentication type\. Specify "NONE" for open access\. Specify "AWS\_IAM" to use IAM permissions\. Values are case insensitive\. | 

## x\-amazon\-apigateway\-auth example<a name="api-gateway-swagger-extensions-auth-example"></a>

The following example sets the authentication type for a REST API method\.

------
#### [ OpenAPI 3\.0\.1 ]

```
{
  "openapi": "3.0.1",
  "info": {
    "title": "openapi3",
    "version": "2018-12-04T05:22:50Z"
  },
  "servers": [
    {
      "url": "https://hs8l62bm7l.execute-api.us-west-2.amazonaws.com/{basePath}",
      "variables": {
        "basePath": {
          "default": "/dev"
        }
      }
    }
  ],
  "security": [
    {
      "api_key": []
    }
  ],
  "paths": {
    "/key": {
      "get": {
        "x-amazon-apigateway-auth": {
          "type": "NONE"
        },
        "security": [
          {
            "api_key": []
          }
        ]
      }
    }
  },
  "components": {
    "securitySchemes": {
      "api_key": {
        "type": "apiKey",
        "name": "x-api-key",
        "in": "header"
      }
    }
  }
}
```

------