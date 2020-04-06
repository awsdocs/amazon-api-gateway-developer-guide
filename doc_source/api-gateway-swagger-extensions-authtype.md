# x\-amazon\-apigateway\-authtype property<a name="api-gateway-swagger-extensions-authtype"></a>

 Specify an optional customer\-defined information describing a Lambda authorizer \(formerly known as a custom authorizer\)\. It is used for API Gateway API import and export without functional impact\. 

 This property is an extended property of the [OpenAPI Security Definitions Operation](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securityDefinitionsObject) object\. 

## x\-amazon\-apigateway\-authtype example<a name="api-gateway-swagger-extensions-authtype-example"></a>

The following example sets the type of a Lambda authorizer using OAuth 2\.

```
    "cust-authorizer" : {
      "type" : "...", // required
      "name" : "...", // name of the identity source header
      "in" : "header", // must be header
      "x-amazon-apigateway-authtype" : "oauth2", // Specifies the authorization mechanism for the client.
      "x-amazon-apigateway-authorizer" : {
          ...
      }
    }
```

The following security definition example specifies authorization using [AWS Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html):

```
  "sigv4" : {
    "type" : "apiKey",
    "name" : "Authorization",
    "in" : "header",
    "x-amazon-apigateway-authtype" : "awsSigv4"
  }
```

## See also<a name="api-gateway-swagger-extensions-authtype-see-also"></a>

[authorizer\.authType](https://docs.aws.amazon.com/apigateway/api-reference/resource/authorizer/#authType)