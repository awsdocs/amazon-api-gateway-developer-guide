# Working with OpenAPI definitions for HTTP APIs<a name="http-api-open-api"></a>

You can define your HTTP API by using an OpenAPI 3\.0 definition file\. Then you can import the definition into API Gateway to create an API\. To learn more about API Gateway extensions to OpenAPI, see [Working with API Gateway extensions to OpenAPI](api-gateway-swagger-extensions.md)\.

## Importing an HTTP API<a name="http-api-import"></a>

### <a name="http-api-import.supported"></a>

You can create an HTTP API by importing an OpenAPI 3\.0 definition file\.

To migrate from a REST API to an HTTP API, you can export your REST API as an OpenAPI 3\.0 definition file\. Then import the API definition as an HTTP API\. To learn more about exporting a REST API, see [Export a REST API from API Gateway](api-gateway-export-api.md)\. 

**Note**  
HTTP APIs support the same AWS variables as REST APIs\. To learn more, see [AWS variables for OpenAPI import](import-api-aws-variables.md)\.

### Import validation information<a name="http-api-import.validation"></a>

As you import an API, API Gateway provides three categories of validation information\.

**Info**  
A property is valid according to the OpenAPI specification, but that property isn’t supported for HTTP APIs\.  
For example, the following OpenAPI 3\.0 snippet produces info on import because HTTP APIs don't support request validation\. API Gateway ignores the `requestBody` and `schema` fields\.  

```
"paths": {
  "/": {
    "get": {
      "x-amazon-apigateway-integration": {
        "type": "AWS_PROXY",
        "httpMethod": "POST",
        "uri": "arn:aws:lambda:us-east-2:123456789012:function:HelloWorld",
        "payloadFormatVersion": "1.0"
      },
      "requestBody": {
        "content": {
          "application/json": {
            "schema": {
              "$ref": "#/components/schemas/Body"
            }
          }
        }
      }
    }
  }
  ...
},
"components": {
  "schemas": {
    "Body": {
      "type": "object",
      "properties": {
        "key": {
          "type": "string"
        }
      }
    }
    ...
  }
  ...
}
```

**Warning**  
A property or structure is invalid according to the OpenAPI specification, but it doesn’t block API creation\. You can specify whether API Gateway should ignore these warnings and continue creating the API, or stop creating the API on warnings\.  
The following OpenAPI 3\.0 document produces warnings on import because HTTP APIs support only Lambda proxy and HTTP proxy integrations\.  

```
"x-amazon-apigateway-integration": {
  "type": "AWS",
  "httpMethod": "POST",
  "uri": "arn:aws:lambda:us-east-2:123456789012:function:HelloWorld",
  "payloadFormatVersion": "1.0"
}
```

**Error**  
The OpenAPI specification is invalid or malformed\. API Gateway can’t create any resources from the malformed document\. You must fix the errors, and then try again\.  
The following API definition produces errors on import because HTTP APIs support only the OpenAPI 3\.0 specification\.  

```
{
  "swagger": "2.0.0",
  "info": {
    "title": "My API",
    "description": "An Example OpenAPI definition for Errors/Warnings/ImportInfo",
    "version": "1.0"
  }
  ...
}
```

### Import an API by using the AWS CLI<a name="http-api-import.example"></a>

The following command imports the OpenAPI 3\.0 definition file `api-definition.json` as an HTTP API\.

**Example**  

```
aws apigatewayv2 import-api --body file://api-definition.json
```

**Example**  
You can import the following example OpenAPI 3\.0 definition to create an HTTP API\.  

```
{
  "openapi": "3.0.1",
  "info": {
    "title": "Example Pet Store",
    "description": "A Pet Store API.",
    "version": "1.0"
  },
  "paths": {
    "/pets": {
      "get": {
        "operationId": "GET HTTP",
        "parameters": [
          {
            "name": "type",
            "in": "query",
            "schema": {
              "type": "string"
            }
          },
          {
            "name": "page",
            "in": "query",
            "schema": {
              "type": "string"
            }
          }
        ],
        "responses": {
          "200": {
            "description": "200 response",
            "headers": {
              "Access-Control-Allow-Origin": {
                "schema": {
                  "type": "string"
                }
              }
            },
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/Pets"
                }
              }
            }
          }
        },
        "x-amazon-apigateway-integration": {
          "type": "HTTP_PROXY",
          "httpMethod": "GET",
          "uri": "http://petstore.execute-api.us-west-1.amazonaws.com/petstore/pets",
          "payloadFormatVersion": 1.0
        }
      },
      "post": {
        "operationId": "Create Pet",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/NewPet"
              }
            }
          },
          "required": true
        },
        "responses": {
          "200": {
            "description": "200 response",
            "headers": {
              "Access-Control-Allow-Origin": {
                "schema": {
                  "type": "string"
                }
              }
            },
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/NewPetResponse"
                }
              }
            }
          }
        },
        "x-amazon-apigateway-integration": {
          "type": "HTTP_PROXY",
          "httpMethod": "POST",
          "uri": "http://petstore.execute-api.us-west-1.amazonaws.com/petstore/pets",
          "payloadFormatVersion": 1.0
        }
      }
    },
    "/pets/{petId}": {
      "get": {
        "operationId": "Get Pet",
        "parameters": [
          {
            "name": "petId",
            "in": "path",
            "required": true,
            "schema": {
              "type": "string"
            }
          }
        ],
        "responses": {
          "200": {
            "description": "200 response",
            "headers": {
              "Access-Control-Allow-Origin": {
                "schema": {
                  "type": "string"
                }
              }
            },
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/Pet"
                }
              }
            }
          }
        },        
        "x-amazon-apigateway-integration": {
          "type": "HTTP_PROXY",
          "httpMethod": "GET",
          "uri": "http://petstore.execute-api.us-west-1.amazonaws.com/petstore/pets/{petId}",
          "payloadFormatVersion": 1.0
        }
      }
    }
  },
  "x-amazon-apigateway-cors": {
    "allowOrigins": [
      "*"
    ],
    "allowMethods": [
      "GET",
      "OPTIONS",
      "POST"
    ],
    "allowHeaders": [
      "x-amzm-header",
      "x-apigateway-header",
      "x-api-key",
      "authorization",
      "x-amz-date",
      "content-type"
    ]
  },
  "components": {
    "schemas": {
      "Pets": {
        "type": "array",
        "items": {
          "$ref": "#/components/schemas/Pet"
        }
      },
      "Empty": {
        "type": "object"
      },
      "NewPetResponse": {
        "type": "object",
        "properties": {
          "pet": {
            "$ref": "#/components/schemas/Pet"
          },
          "message": {
            "type": "string"
          }
        }
      },
      "Pet": {
        "type": "object",
        "properties": {
          "id": {
            "type": "string"
          },
          "type": {
            "type": "string"
          },
          "price": {
            "type": "number"
          }
        }
      },
      "NewPet": {
        "type": "object",
        "properties": {
          "type": {
            "$ref": "#/components/schemas/PetType"
          },
          "price": {
            "type": "number"
          }
        }
      },
      "PetType": {
        "type": "string",
        "enum": [
          "dog",
          "cat",
          "fish",
          "bird",
          "gecko"
        ]
      }
    }
  }
}
```