# x\-amazon\-apigateway\-authtype property<a name="api-gateway-swagger-extensions-authtype"></a>

For REST APIs, this extension can be used to define a custom type of a Lambda authorizer\. In this case, the value is free\-form\. For example, an API may have multiple Lambda authorizers that use different internal schemes\. You can use this extension to identify the internal scheme of a Lambda authorizer\.

More commonly, in HTTP APIs and REST APIs, it can also be used as a way to define IAM authorization across several operations that share the same security scheme\. In this case, the term `awsSigv4` is a reserved term, along with any term prefixed by `aws`\.

This extension applies to the `apiKey` type security scheme in [OpenAPI 2](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securitySchemeObject) and [OpenAPI 3\.](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.1.md#securitySchemeObject) 

## x\-amazon\-apigateway\-authtype example<a name="api-gateway-swagger-extensions-authtype-example"></a>

The following OpenAPI 3 example defines IAM authorization across multiple resources in a REST API or HTTP API:

```
{
  "openapi" : "3.0.1",
  "info" : {
    "title" : "openapi3",
    "version" : "1.0"
  },
  "paths" : {
    "/operation1" : {
      "get" : {
        "responses" : {
          "default" : {
            "description" : "Default response"
          }
        },
        "security" : [ {
          "sigv4Reference" : [ ]
        } ]
      }
    },
    "/operation2" : {
      "get" : {
        "responses" : {
          "default" : {
            "description" : "Default response"
          }
        },
        "security" : [ {
          "sigv4Reference" : [ ]
        } ]
      }
    }
  },
  "components" : {
    "securitySchemes" : {
      "sigv4Reference" : {
        "type" : "apiKey",
        "name" : "Authorization",
        "in" : "header",
        "x-amazon-apigateway-authtype": "awsSigv4"
      }
    }
  }
}
```

The following OpenAPI 3 example defines a Lambda authorizer with a custom scheme for a REST API:

```
{
  "openapi" : "3.0.1",
  "info" : {
    "title" : "openapi3 for REST API",
    "version" : "1.0"
  },
  "paths" : {
    "/protected-by-lambda-authorizer" : {
      "get" : {
        "responses" : {
          "200" : {
            "description" : "Default response"
          }
        },
        "security" : [ {
          "myAuthorizer" : [ ]
        } ]
      }
    }
  },
  "components" : {
    "securitySchemes" : {
      "myAuthorizer" : {
        "type" : "apiKey",
        "name" : "Authorization",
        "in" : "header",
        "x-amazon-apigateway-authorizer" : {
          "identitySource" : "method.request.header.Authorization",
          "authorizerUri" : "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:account-id:function:function-name/invocations",
          "authorizerResultTtlInSeconds" : 300,
          "type" : "request",
          "enableSimpleResponses" : false
        },
        "x-amazon-apigateway-authtype": "Custom scheme with corporate claims"
      }
    }
  },
  "x-amazon-apigateway-importexport-version" : "1.0"
}
```

## See also<a name="api-gateway-swagger-extensions-authtype-see-also"></a>

[authorizer\.authType](https://docs.aws.amazon.com/apigateway/api-reference/resource/authorizer/#authType)