# Access Binary Files in Amazon S3 through an API Gateway API<a name="api-gateway-content-encodings-examples-image-s3"></a>

The following examples show the Swagger file used to access images in Amazon S3, how to download an image from Amazon S3, and how to upload an image to Amazon S3\. 

**Topics**
+ [Swagger File of a Sample API to Access Images in Amazon S3](#api-gateway-content-encodings-example-image-s3-swagger-file)
+ [Download an Image from Amazon S3](#api-gateway-content-encodings-example-download-image-from-s3)
+ [Upload an Image to Amazon S3](#api-gateway-content-encodings-example-upload-image-to-s3)

## Swagger File of a Sample API to Access Images in Amazon S3<a name="api-gateway-content-encodings-example-image-s3-swagger-file"></a>

The following Swagger file shows a sample API that illustrates downloading an image file from Amazon S3 and uploading an image file to Amazon S3\. This API exposes the `GET /s3?key={file-name}` and `PUT /s3?key={file-name}` methods for downloading and uploading a specified image file\. The `GET` method returns the image file as a Base64\-encoded string as part of a JSON output, following the supplied mapping template, in a 200 OK response\. The `PUT` method takes a raw binary blob as input and returns a 200 OK response with an empty payload\.

```
{
  "swagger": "2.0",
  "info": {
    "version": "2016-10-21T17:26:28Z",
    "title": "ApiName"
  },
  "host": "abcdefghi.execute-api.us-east-1.amazonaws.com",
  "basePath": "/v1",
  "schemes": [
    "https"
  ],
  "paths": {
    "/s3": {
      "get": {
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "key",
            "in": "query",
            "required": false,
            "type": "string"
          }
        ],
        "responses": {
          "200": {
            "description": "200 response",
            "schema": {
              "$ref": "#/definitions/Empty"
            }
          },
          "500": {
            "description": "500 response"
          }
        },
        "x-amazon-apigateway-integration": {
          "credentials": "arn:aws:iam::123456789012:role/binarySupportRole",
          "responses": {
            "default": {
              "statusCode": "500"
            },
            "2\\d{2}": {
              "statusCode": "200"            }
          },
          "requestParameters": {
            "integration.request.path.key": "method.request.querystring.key"
          },
          "uri": "arn:aws:apigateway:us-west-2:s3:path/{key}",
          "passthroughBehavior": "when_no_match",
          "httpMethod": "GET",
          "type": "aws"
        }
      },
      "put": {
        "produces": [
          "application/json", "application/octet-stream"
        ],
        "parameters": [
          {
            "name": "key",
            "in": "query",
            "required": false,
            "type": "string"
          }
        ],
        "responses": {
          "200": {
            "description": "200 response",
            "schema": {
              "$ref": "#/definitions/Empty"
            }
          },
          "500": {
            "description": "500 response"
          }
        },
        "x-amazon-apigateway-integration": {
          "credentials": "arn:aws:iam::123456789012:role/binarySupportRole",
          "responses": {
            "default": {
              "statusCode": "500"
            },
            "2\\d{2}": {
              "statusCode": "200"
            }
          },
          "requestParameters": {
            "integration.request.path.key": "method.request.querystring.key"
          },
          "uri": "arn:aws:apigateway:us-west-2:s3:path/{key}",
          "passthroughBehavior": "when_no_match",
          "httpMethod": "PUT",
          "type": "aws",
          "contentHandling" : "CONVERT_TO_BINARY"
        }
      }
    }
  },
  "x-amazon-apigateway-binary-media-types" : ["application/octet-stream", "image/jpeg"],
  "definitions": {
    "Empty": {
      "type": "object",
      "title": "Empty Schema"
    }
  }
}
```

## Download an Image from Amazon S3<a name="api-gateway-content-encodings-example-download-image-from-s3"></a>

To download an image file \(`image.jpg`\) as a binary blob from Amazon S3:

```
GET /v1/s3?key=image.jpg HTTP/1.1
Host: abcdefghi.execute-api.us-east-1.amazonaws.com
Content-Type: application/json
Accept: application/octet-stream
```

The successful response looks like this:

```
200 OK HTTP/1.1

[raw bytes]
```

The raw bytes are returned because the `Accept` header is set to a binary media type of `application/octet-stream` and binary support is enabled for the API\. 

Alternatively, to download an image file \(`image.jpg`\) as a Base64\-encoded string, formatted as a JSON property, from Amazon S3, add a response template to the 200 integration response like this, as shown in the bold\-faced Swagger definition block below:

```
        "x-amazon-apigateway-integration": {
          "credentials": "arn:aws:iam::123456789012:role/binarySupportRole",
          "responses": {
            "default": {
              "statusCode": "500"
            },
            "2\\d{2}": {
              "statusCode": "200",
              "responseTemplates": {
                "application/json": "{\n   \"image\": \"$input.body\"\n}"
              }
            }
          },
```

The request to download the image file looks like this:

```
GET /v1/s3?key=image.jpg HTTP/1.1
Host: abcdefghi.execute-api.us-east-1.amazonaws.com
Content-Type: application/json
Accept: application/json
```

The successful response looks like this:

```
200 OK HTTP/1.1

{
  "image": "W3JhdyBieXRlc10="
}
```

## Upload an Image to Amazon S3<a name="api-gateway-content-encodings-example-upload-image-to-s3"></a>

To upload an image file \(`image.jpg`\) as a binary blob to Amazon S3:

```
PUT /v1/s3?key=image.jpg HTTP/1.1
Host: abcdefghi.execute-api.us-east-1.amazonaws.com
Content-Type: application/octet-stream
Accept: application/json

[raw bytes]
```

The successful response looks like this:

```
200 OK HTTP/1.1        
```

To upload an image file \(`image.jpg`\) as a Base64\-encoded string to Amazon S3:

```
PUT /v1/s3?key=image.jpg HTTP/1.1
Host: abcdefghi.execute-api.us-east-1.amazonaws.com
Content-Type: application/json
Accept: application/json

W3JhdyBieXRlc10=
```

Notice that the input payload must be a Base64\-encoded string, because the `Content-Type` header value is set to `application/json`\. The successful response looks like this:

```
200 OK HTTP/1.1
```