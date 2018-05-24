# Set up an Edge\-Optimized API by Importing Swagger Definitions<a name="create-api-using-swagger"></a>

You can set up an API in API Gateway by specifying Swagger definitions of appropriate API Gateway API entities and importing the Swagger definitions into API Gateway\.

The following Swagger definitions describe the simple API, exposing only the `GET /` method integrated with an HTTP endpoint of the PetStore website in the backend, and returning a `200 OK` response\.

```
{
  "swagger": "2.0",
  "info": {
    "title": "Simple PetStore (Swagger)"
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

The following procedure describes how to import these Swagger definitions into API Gateway using the API Gateway console\.

**To import the simple API Swagger definitions using the API Gateway console**

1. Sign in to the API Gateway console\.

1. Choose **Create API**\.

1. Choose **Import from Swagger**\.

1. If you saved the preceding Swagger definitions in a file, choose **Select Swagger File**\. You can also copy the Swagger definitions and paste them into the import text editor\.

1. Choose **Import** to finish importing the Swagger definitions\.

To import the Swagger definitions using the API Gateway REST API, call the [restapi:import](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) action, supplying the preceding Swagger definitions as the payload\. For more information, see the example in the Remarks section of the `restapi:import` topic\. 

To import the Swagger definitions using the AWS CLI, save the Swagger definitions into a file and then run the following command, assuming that you use the `us-west-2` region and the absolute Swagger file path is `file:///path/to/API_Swagger_template.json`:

```
aws apigateway import-rest-api --body 'file:///path/to/API_Swagger_template.json' --region us-west-2
```