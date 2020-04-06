# Publish API documentation using the API Gateway REST API<a name="api-gateway-documenting-api-quick-start-publishing"></a>

To publish the documentation for an API, create, update, or get a documentation snapshot, and then associate the documentation snapshot with an API stage\. When creating a documentation snapshot, you can also associate it with an API stage at the same time\.

**Topics**
+ [Create a documentation snapshot and associate it with an API stage](#api-gateway-documenting-api-publishing-create-documentation-version-with-stage)
+ [Create a documentation snapshot](#api-gateway-documenting-api-publishing-create-documentation-version)
+ [Update a documentation snapshot](#api-gateway-documenting-api-publishing-update-documentation-version)
+ [Get a documentation snapshot](#api-gateway-documenting-api-publishing-get-documentation-version)
+ [Associate a documentation snapshot with an API stage](#api-gateway-documenting-api-publishing-stage-association)
+ [Download a documentation snapshot associated with a stage](#api-gateway-documenting-api-publishing-export-documentation-version)

## Create a documentation snapshot and associate it with an API stage<a name="api-gateway-documenting-api-publishing-create-documentation-version-with-stage"></a>

To create a snapshot of an API's documentation parts and associate it with an API stage at the same time, submit the following `POST` request:

```
POST /restapis/restapi_id/documentation/versions HTTP/1.1
Host: apigateway.region.amazonaws.com
Content-Type: application/json
X-Amz-Date: YYYYMMDDTttttttZ
Authorization: AWS4-HMAC-SHA256 Credential=access_key_id/YYYYMMDD/region/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=sigv4_secret

{
    "documentationVersion" : "1.0.0",
    "stageName": "prod",
    "description" : "My API Documentation v1.0.0"
}
```

If successful, the operation returns a `200 OK` response, containing the newly created `DocumentationVersion` instance as the payload\.

Alternatively, you can create a documentation snapshot without associating it with an API stage first and then call [restapi:update](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/) to associate the snapshot with a specified API stage\. You can also update or query an existing documentation snapshot and then update its stage association\. We show the steps in the next four sections\.

## Create a documentation snapshot<a name="api-gateway-documenting-api-publishing-create-documentation-version"></a>

To create a snapshot of an API's documentation parts, create a new [DocumentationVersion](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-version/) resource and add it to the [DocumentationVersions](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-versions/) collection of the API:

```
POST /restapis/restapi_id/documentation/versions HTTP/1.1
Host: apigateway.region.amazonaws.com
Content-Type: application/json
X-Amz-Date: YYYYMMDDTttttttZ
Authorization: AWS4-HMAC-SHA256 Credential=access_key_id/YYYYMMDD/region/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=sigv4_secret

{
    "documentationVersion" : "1.0.0",
    "description" : "My API Documentation v1.0.0"
}
```

If successful, the operation returns a `200 OK` response, containing the newly created `DocumentationVersion` instance as the payload\.

## Update a documentation snapshot<a name="api-gateway-documenting-api-publishing-update-documentation-version"></a>

You can only update a documentation snapshot by modifying the `description` property of the corresponding [DocumentationVersion](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-version/) resource\. The following example shows how to update the description of the documentation snapshot as identified by its version identifier, `version`, e\.g\., `1.0.0`\.

```
PATCH /restapis/restapi_id/documentation/versions/version HTTP/1.1
Host: apigateway.region.amazonaws.com
Content-Type: application/json
X-Amz-Date: YYYYMMDDTttttttZ
Authorization: AWS4-HMAC-SHA256 Credential=access_key_id/YYYYMMDD/region/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=sigv4_secret

{
    "patchOperations": [{
        "op": "replace",
        "path": "/description",
        "value": "My API for testing purposes."
    }]
}
```

If successful, the operation returns a `200 OK` response, containing the updated `DocumentationVersion` instance as the payload\. 

## Get a documentation snapshot<a name="api-gateway-documenting-api-publishing-get-documentation-version"></a>

To get a documentation snapshot, submit a `GET` request against the specified [DocumentationVersion](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-version/) resource\. The following example shows how to get a documentation snapshot of a given version identifier, 1\.0\.0\.

```
GET /restapis/<restapi_id>/documentation/versions/1.0.0 HTTP/1.1
Host: apigateway.region.amazonaws.com
Content-Type: application/json
X-Amz-Date: YYYYMMDDTttttttZ
Authorization: AWS4-HMAC-SHA256 Credential=access_key_id/YYYYMMDD/region/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=sigv4_secret
```

## Associate a documentation snapshot with an API stage<a name="api-gateway-documenting-api-publishing-stage-association"></a>

To publish the API documentation, associate a documentation snapshot with an API stage\. You must have already created an API stage before associating the documentation version with the stage\.

To associate a documentation snapshot with an API stage using the [API Gateway REST API](https://docs.aws.amazon.com/apigateway/api-reference/), call the [stage:update](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/) operation to set the desired documentation version on the `stage.documentationVersion` property:

```
PATCH /restapis/RESTAPI_ID/stages/STAGE_NAME
Host: apigateway.region.amazonaws.com
Content-Type: application/json
X-Amz-Date: YYYYMMDDTttttttZ
Authorization: AWS4-HMAC-SHA256 Credential=access_key_id/YYYYMMDD/region/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=sigv4_secret

{
    "patchOperations": [{
        "op": "replace",
        "path": "/documentationVersion",
        "value": "VERSION_IDENTIFIER"
    }]
}
```

## Download a documentation snapshot associated with a stage<a name="api-gateway-documenting-api-publishing-export-documentation-version"></a>

After a version of the documentation parts is associated with a stage, you can export the documentation parts together with the API entity definitions, to an external file, using the API Gateway console, the API Gateway REST API, one of its SDKs, or the AWS CLI for API Gateway\. The process is the same as for exporting the API\. The exported file format can be JSON or YAML\. 

Using the API Gateway REST API, you can also explicitly set the `extension=documentation,integrations,authorizers` query parameter to include the API documentation parts, API integrations and authorizers in an API export\. By default, documentation parts are included, but integrations and authorizers are excluded, when you export an API\. The default output from an API export is suited for distribution of the documentation\.

To export the API documentation in an external JSON OpenAPI file using the API Gateway REST API, submit the following `GET` request:

```
GET /restapis/restapi_id/stages/stage_name/exports/swagger?extensions=documentation HTTP/1.1
Accept: application/json
Host: apigateway.region.amazonaws.com
Content-Type: application/json
X-Amz-Date: YYYYMMDDTttttttZ
Authorization: AWS4-HMAC-SHA256 Credential=access_key_id/YYYYMMDD/region/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=sigv4_secret
```

Here, the `x-amazon-apigateway-documentation` object contains the documentation parts and the API entity definitions contains the documentation properties supported by OpenAPI\. The output does not include details of integration or Lambda authorizers \(formerly known as custom authorizers\)\. To include both details, set `extensions=integrations,authorizers,documentation`\. To include details of integrations but not of authorizers, set `extensions=integrations,documentation`\. 

You must set the `Accept:application/json` header in the request to output the result in a JSON file\. To produce the YAML output, change the request header to `Accept:application/yaml`\. 

As an example, we will look at an API that exposes a simple `GET` method on the root resource \(`/`\)\. This API has four API entities defined in an OpenAPI definition file, one for each of the `API`, `MODEL`, `METHOD`, and `RESPONSE` types\. A documentation part has been added to each of the `API`, `METHOD`, and `RESPONSE` entities\. Calling the preceding documentation\-exporting command, we get the following output, with the documentation parts listed within the `x-amazon-apigateway-documentation` object as an extension to a standard OpenAPI file\.

------
#### [ OpenAPI 3\.0 ]

```
{
   "openapi": "3.0.0",
   "info": {
      "description": "API info description",
      "version": "2016-11-22T22:39:14Z",
      "title": "doc",
      "x-bar": "API info x-bar"
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
            },
            "x-example": "x- Method example"
         },
         "x-bar": "resource x-bar"
      }
   },
   "x-amazon-apigateway-documentation": {
      "version": "1.0.0",
      "createdDate": "2016-11-22T22:41:40Z",
      "documentationParts": [
         {
            "location": {
               "type": "API"
            },
            "properties": {
               "description": "API description",
               "foo": "API foo",
               "x-bar": "API x-bar",
               "info": {
                  "description": "API info description",
                  "version": "API info version",
                  "foo": "API info foo",
                  "x-bar": "API info x-bar"
               }
            }
         },
         {
            "location": {
               "type": "METHOD",
               "method": "GET"
            },
            "properties": {
               "description": "Method description.",
               "x-example": "x- Method example",
               "foo": "Method foo",
               "info": {
                  "version": "method info version",
                  "description": "method info description",
                  "foo": "method info foo"
               }
            }
         },
         {
            "location": {
               "type": "RESOURCE"
            },
            "properties": {
               "description": "resource description",
               "foo": "resource foo",
               "x-bar": "resource x-bar",
               "info": {
                  "description": "resource info description",
                  "version": "resource info version",
                  "foo": "resource info foo",
                  "x-bar": "resource info x-bar"
               }
            }
         }
      ]
   },
   "x-bar": "API x-bar",
   "servers": [
      {
         "url": "https://rznaap68yi.execute-api.ap-southeast-1.amazonaws.com/{basePath}"
         "variables": {
            "basePath": {
              "default": "/test"
            }
         }
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
{
  "swagger" : "2.0",
  "info" : {
    "description" : "API info description",
    "version" : "2016-11-22T22:39:14Z",
    "title" : "doc",
    "x-bar" : "API info x-bar"
  },
  "host" : "rznaap68yi.execute-api.ap-southeast-1.amazonaws.com",
  "basePath" : "/test",
  "schemes" : [ "https" ],
  "paths" : {
    "/" : {
      "get" : {
        "description" : "Method description.",
        "produces" : [ "application/json" ],
        "responses" : {
          "200" : {
            "description" : "200 response",
            "schema" : {
              "$ref" : "#/definitions/Empty"
            }
          }
        },
        "x-example" : "x- Method example"
      },
      "x-bar" : "resource x-bar"
    }
  },
  "definitions" : {
    "Empty" : {
      "type" : "object",
      "title" : "Empty Schema"
    }
  },
  "x-amazon-apigateway-documentation" : {
    "version" : "1.0.0",
    "createdDate" : "2016-11-22T22:41:40Z",
    "documentationParts" : [ {
      "location" : {
        "type" : "API"
      },
      "properties" : {
        "description" : "API description",
        "foo" : "API foo",
        "x-bar" : "API x-bar",
        "info" : {
          "description" : "API info description",
          "version" : "API info version",
          "foo" : "API info foo",
          "x-bar" : "API info x-bar"
        }
      }
    }, {
      "location" : {
        "type" : "METHOD",
        "method" : "GET"
      },
      "properties" : {
        "description" : "Method description.",
        "x-example" : "x- Method example",
        "foo" : "Method foo",
        "info" : {
          "version" : "method info version",
          "description" : "method info description",
          "foo" : "method info foo"
        }
      }
    }, {
      "location" : {
        "type" : "RESOURCE"
      },
      "properties" : {
        "description" : "resource description",
        "foo" : "resource foo",
        "x-bar" : "resource x-bar",
        "info" : {
          "description" : "resource info description",
          "version" : "resource info version",
          "foo" : "resource info foo",
          "x-bar" : "resource info x-bar"
        }
      }
    } ]
  },
  "x-bar" : "API x-bar"
}
```

------

For an OpenAPI\-compliant attribute defined in the `properties` map of a documentation part, API Gateway inserts the attribute into the associated API entity definition\. An attribute of `x-something` is a standard OpenAPI extension\. This extension gets propagated into the API entity definition\. For example, see the `x-example` attribute for the `GET` method\. An attribute like `foo` is not part of the OpenAPI specification and is not injected into its associated API entity definitions\. 

If a documentation\-rendering tool \(e\.g\., [OpenAPI UI](http://swagger.io/swagger-ui/)\) parses the API entity definitions to extract documentation attributes, any non OpenAPI\-compliant `properties` attributes of a `DocumentationPart`' instance are not available for the tool\. However, if a documentation\-rendering tool parses the `x-amazon-apigateway-documentation` object to get content, or if the tool calls [restapi:documentation\-parts](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-documentation-parts/) and [documenationpart:by\-id](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/documentationpart-by-id/) to retrieve documentation parts from API Gateway, all the documentation attributes are available for the tool to display\.

To export the documentation with API entity definitions containing integration details to a JSON OpenAPI file, submit the following `GET` request:

```
GET /restapis/restapi_id/stages/stage_name/exports/swagger?extensions=integrations,documentation HTTP/1.1
Accept: application/json
Host: apigateway.region.amazonaws.com
Content-Type: application/json
X-Amz-Date: YYYYMMDDTttttttZ
Authorization: AWS4-HMAC-SHA256 Credential=access_key_id/YYYYMMDD/region/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=sigv4_secret
```

To export the documentation with API entity definitions containing details of integrations and authorizers to a YAML OpenAPI file, submit the following `GET` request:

```
GET /restapis/restapi_id/stages/stage_name/exports/swagger?extensions=integrations,authorizers,documentation HTTP/1.1
Accept: application/yaml
Host: apigateway.region.amazonaws.com
Content-Type: application/json
X-Amz-Date: YYYYMMDDTttttttZ
Authorization: AWS4-HMAC-SHA256 Credential=access_key_id/YYYYMMDD/region/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=sigv4_secret
```

To use the API Gateway console to export and download the published documentation of an API, follow the instructions in [Export REST API using the API Gateway console](api-gateway-export-api.md#api-gateway-export-api-from-console)\. 