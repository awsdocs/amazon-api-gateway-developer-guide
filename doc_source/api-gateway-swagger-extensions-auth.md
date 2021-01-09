# x\-amazon\-apigateway\-auth object<a name="api-gateway-swagger-extensions-auth"></a>

Defines an authorization type to be applied for authorization of method invocations in API Gateway\.


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| type | string | Specifies the authorization type\. Specify "NONE" for open access\. Specify "AWS\_IAM" to use IAM permissions\. Values are case insensitive\. | 

## x\-amazon\-apigateway\-auth example<a name="api-gateway-swagger-extensions-auth-example"></a>

The following example sets the authorization type for an API method\.

------
#### [ OpenAPI 3\.0\.1 ]

```
{
  "openapi": "3.0.1",
  "info": {
    "title": "openapi3",
    "version": "1.0"
  },
  "paths": {
    "/protected-by-iam": {
      "get": {
        "x-amazon-apigateway-auth": {
          "type": "AWS_IAM"
        }
      }
    }
  }
}
```

------