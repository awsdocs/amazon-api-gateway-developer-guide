# Set up an edge\-optimized API by importing OpenAPI definitions<a name="create-api-using-swagger"></a>

You can set up an API in API Gateway by specifying OpenAPI definitions of appropriate API Gateway API entities and importing the OpenAPI definitions into API Gateway\.

The following OpenAPI definitions describe the simple API, exposing only the `GET /` method integrated with an HTTP endpoint of the PetStore website in the backend, and returning a `200 OK` response\.

------
#### [ OpenAPI 2\.0 ]

```
{
  "swagger": "2.0",
  "info": {
    "title": "Simple PetStore (OpenAPI)"
  },
  "schemes": [
    "https"
  ],
  "paths": {
    "/pets": {
      "get": {
        "responses": {
          "200": {
            "description": "200 response"
          }
        },
        "x-amazon-apigateway-integration": {
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/pets",
          "passthroughBehavior": "when_no_match",
          "httpMethod": "GET",
          "type": "http"
        }
      }
    },
    "/pets/{petId}": {
      "get": {
        "parameters": [
          {
            "name": "petId",
            "in": "path",
            "required": true,
            "type": "string"
          }
        ],
        "responses": {
          "200": {
            "description": "200 response"
          }
        },
        "x-amazon-apigateway-integration": {
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "requestParameters": {
            "integration.request.path.id": "method.request.path.petId"
          },
          "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/pets/{id}",
          "passthroughBehavior": "when_no_match",
          "httpMethod": "GET",
          "type": "http"
        }
      }
    }
  }
}
```

------

The following procedure describes how to import these OpenAPI definitions into API Gateway using the API Gateway console\.

**To import the simple OpenAPI definitions using the API Gateway console**

1. Sign in to the API Gateway console\.

1. Choose **Create API**\.

1. Choose **Import from OpenAPI**\.

1. If you saved the preceding OpenAPI definitions in a file, choose **Select OpenAPI File**\. You can also copy the OpenAPI definitions and paste them into the import text editor\.

1. Choose **Import** to finish importing the OpenAPI definitions\.

To import the OpenAPI definitions using the AWS CLI, save the OpenAPI definitions into a file and then run the following command, assuming that you use the `us-west-2` region and the absolute OpenAPI file path is `file:///path/to/API_OpenAPI_template.json`:

```
aws apigateway import-rest-api --body 'file:///path/to/API_OpenAPI_template.json' --region us-west-2
```