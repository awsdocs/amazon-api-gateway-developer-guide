# Import API documentation<a name="api-gateway-documenting-api-quick-start-import-export"></a>

 As with importing API entity definitions, you can import documentation parts from an external OpenAPI file into an API in API Gateway\. You specify the to\-be\-imported documentation parts within the [x\-amazon\-apigateway\-documentation object](api-gateway-swagger-extensions-documentation.md) extension in a valid OpenAPI definition file\. Importing documentation does not alter the existing API entity definitions\.

You have an option to merge the newly specified documentation parts into existing documentation parts in API Gateway or to overwrite the existing documentation parts\. In the `MERGE` mode, a new documentation part defined in the OpenAPI file is added to the `DocumentationParts` collection of the API\. If an imported `DocumentationPart` already exists, an imported attribute replaces the existing one if the two are different\. Other existing documentation attributes remain unaffected\. In the `OVERWRITE` mode, the entire `DocumentationParts` collection is replaced according to the imported OpenAPI definition file\.

## Importing documentation parts using the API Gateway REST API<a name="api-gateway-importing-api-with-swagger-file-using-rest-api"></a>

To import API documentation using the API Gateway REST API, call the [documentationpart:import](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/documentationpart-import/) operation\. The following example shows how to overwrite existing documentation parts of an API with a single `GET / ` method, returning a `200 OK` response when successful\.

------
#### [ OpenAPI 3\.0 ]

```
PUT /restapis/<restapi_id>/documentation/parts&mode=overwrite&failonwarnings=true
Host: apigateway.region.amazonaws.com
Content-Type: application/json
X-Amz-Date: YYYYMMDDTttttttZ
Authorization: AWS4-HMAC-SHA256 Credential=access_key_id/YYYYMMDD/region/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=sigv4_secret

{
   "openapi": "3.0.0",
   "info": {
      "description": "description",
      "version": "1",
      "title": "doc"
   },
   "paths": {
      "/": {
         "get": {
            "description": "Method description.",
            "responses": {
               "200": {
                  "description": "200 response",
                  "content": {
                     "application/json": {
                        "schema": {
                           "$ref": "#/components/schemas/Empty"
                        }
                     }
                  }
               }
            }
         }
      }
   },
   "x-amazon-apigateway-documentation": {
      "version": "1.0.3",
      "documentationParts": [
         {
            "location": {
               "type": "API"
            },
            "properties": {
               "description": "API description",
               "info": {
                  "description": "API info description 4",
                  "version": "API info version 3"
               }
            }
         },
         {
            "location": {
               "type": "METHOD",
               "method": "GET"
            },
            "properties": {
               "description": "Method description."
            }
         },
         {
            "location": {
               "type": "MODEL",
               "name": "Empty"
            },
            "properties": {
               "title": "Empty Schema"
            }
         },
         {
            "location": {
               "type": "RESPONSE",
               "method": "GET",
               "statusCode": "200"
            },
            "properties": {
               "description": "200 response"
            }
         }
      ]
   },
   "servers": [
      {
         "url": "/"
      }
   ],
   "components": {
      "schemas": {
         "Empty": {
            "type": "object",
            "title": "Empty Schema"
         }
      }
   }
}
```

------
#### [ OpenAPI 2\.0 ]

```
PUT /restapis/<restapi_id>/documentation/parts&mode=overwrite&failonwarnings=true
Host: apigateway.region.amazonaws.com
Content-Type: application/json
X-Amz-Date: YYYYMMDDTttttttZ
Authorization: AWS4-HMAC-SHA256 Credential=access_key_id/YYYYMMDD/region/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=sigv4_secret

{
  "swagger": "2.0",
  "info": {
    "description": "description",
    "version": "1",
    "title": "doc"
  },
  "host": "",
  "basePath": "/",
  "schemes": [
    "https"
  ],
  "paths": {
    "/": {
      "get": {
        "description": "Method description.",
        "produces": [
          "application/json"
        ],
        "responses": {
          "200": {
            "description": "200 response",
            "schema": {
              "$ref": "#/definitions/Empty"
            }
          }
        }
      }
    }
  },
  "definitions": {
    "Empty": {
      "type": "object",
      "title": "Empty Schema"
    }
  },
  "x-amazon-apigateway-documentation": {
    "version": "1.0.3",
    "documentationParts": [
      {
        "location": {
          "type": "API"
        },
        "properties": {
          "description": "API description",
          "info": {
            "description": "API info description 4",
            "version": "API info version 3"
          }
        }
      },
      {
        "location": {
          "type": "METHOD",
          "method": "GET"
        },
        "properties": {
          "description": "Method description."
        }
      },
      {
        "location": {
          "type": "MODEL",
          "name": "Empty"
        },
        "properties": {
          "title": "Empty Schema"
        }
      },
      {
        "location": {
          "type": "RESPONSE",
          "method": "GET",
          "statusCode": "200"
        },
        "properties": {
          "description": "200 response"
        }
      }
    ]
  }
}
```

------

When successful, this request returns a 200 OK response containing the imported `DocumentationPartId` in the payload\.

```
{
  "ids": [
    "kg3mth",
    "796rtf",
    "zhek4p",
    "5ukm9s"
  ]
}
```

In addition, you can also call [restapi:import](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) or [restapi:put](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-put/), supplying the documentation parts in the `x-amazon-apigateway-documentation` object as part of the input OpenAPI file of the API definition\. To exclude the documentation parts from the API import, set `ignore=documentation` in the request query parameters\.

## Importing documentation parts using the API Gateway console<a name="api-gateway-importing-api-with-swagger-file-using-console"></a>

The following instructions describe how to import documentation parts\.

**To use the console to import documentation parts of an API from an external file**

1. Choose **Documentation** for the API from the main navigation pane on the console\.

1. Choose **Import Documentation** in the **Documentation** pane\.

1. Choose **Select OpenAPI File** to load a file from a drive, or copy and paste a file contents into the file view\. For an example, see the payload of the example request in [Importing documentation parts using the API Gateway REST API](#api-gateway-importing-api-with-swagger-file-using-rest-api)\.

1. Optionally, choose **Fail on warnings** or **Ignore warnings**, and choose `Merge` or `Overwrite` from **Import mode**\.

1. Choose **Import**\.