# Enabling binary support using the API Gateway REST API<a name="api-gateway-payload-encodings-configure-with-control-service-api"></a>

The following tasks show how to enable binary support using the API Gateway REST API calls\.

**Topics**
+ [Add and update supported binary media types to an API](#api-gateway-payload-encodings-setup-with-api-set-encodings-map)
+ [Configure request payload conversions](#api-gateway-payload-encodings-setup-with-api-set-integration-request-encoding)
+ [Configure response payload conversions](#api-gateway-payload-encodings-setup-with-api-set-integration-response-encoding)
+ [Convert binary data to text data](#api-gateway-payload-encodings-convert-binary-to-string)
+ [Convert text data to a binary payload](#api-gateway-payload-encodings-convert-string-to-binary)
+ [Pass through a binary payload](#api-gateway-payload-encodings-pass-binary-as-is)

## Add and update supported binary media types to an API<a name="api-gateway-payload-encodings-setup-with-api-set-encodings-map"></a>

To enable API Gateway to support a new binary media type, you must add the binary media type to the `binaryMediaTypes` list of the `RestApi` resource\. For example, to have API Gateway handle JPEG images, submit a `PATCH` request to the `RestApi` resource: 

```
PATCH /restapis/<restapi_id>

{
  "patchOperations" : [ {
    "op" : "add",
    "path" : "/binaryMediaTypes/image~1jpeg"
  } 
 ]
}
```

The MIME type specification of `image/jpeg` that is part of the `path` property value is escaped as `image~1jpeg`\.

To update the supported binary media types, replace or remove the media type from the `binaryMediaTypes` list of the `RestApi` resource\. For example, to change binary support from JPEG files to raw bytes, submit a `PATCH` request to the `RestApi` resource, as follows: 

```
PATCH /restapis/<restapi_id>

{
  "patchOperations" : [{
    "op" : "replace",
    "path" : "/binaryMediaTypes/image~1jpeg",
    "value" : "application/octet-stream"
  },
  {
    "op" : "remove",
    "path" : "/binaryMediaTypes/image~1jpeg"
  }]
}
```

## Configure request payload conversions<a name="api-gateway-payload-encodings-setup-with-api-set-integration-request-encoding"></a>

If the endpoint requires a binary input, set the `contentHandling` property of the `Integration` resource to `CONVERT_TO_BINARY`\. To do so, submit a `PATCH` request, as follows: 

```
PATCH /restapis/<restapi_id>/resources/<resource_id>/methods/<http_method>/integration

{
  "patchOperations" : [ {
    "op" : "replace",
    "path" : "/contentHandling",
    "value" : "CONVERT_TO_BINARY"
  }]
}
```

## Configure response payload conversions<a name="api-gateway-payload-encodings-setup-with-api-set-integration-response-encoding"></a>

If the client accepts the result as a binary blob instead of a base64\-encoded payload returned from the endpoint, set the `contentHandling` property of the `IntegrationResponse` resource to `CONVERT_TO_BINARY`\. To do this, submit a `PATCH` request, as follows:

```
PATCH /restapis/<restapi_id>/resources/<resource_id>/methods/<http_method>/integration/responses/<status_code>

{
  "patchOperations" : [ {
    "op" : "replace",
    "path" : "/contentHandling",
    "value" : "CONVERT_TO_BINARY"
  }]
}
```

## Convert binary data to text data<a name="api-gateway-payload-encodings-convert-binary-to-string"></a>

To send binary data as a JSON property of the input to AWS Lambda or Kinesis through API Gateway, do the following: 

1. Enable the binary payload support of the API by adding the new binary media type of `application/octet-stream` to the API's `binaryMediaTypes` list\. 

   ```
   PATCH /restapis/<restapi_id>
   
   {
     "patchOperations" : [ {
       "op" : "add",
       "path" : "/binaryMediaTypes/application~1octet-stream"
     } 
    ]
   }
   ```

1. Set `CONVERT_TO_TEXT` on the `contentHandling` property of the `Integration` resource and provide a mapping template to assign the base64\-encoded string of the binary data to a JSON property\. In the following example, the JSON property is `body` and `$input.body` holds the base64\-encoded string\.

   ```
   PATCH /restapis/<restapi_id>/resources/<resource_id>/methods/<http_method>/integration
   
   {
     "patchOperations" : [
       {
         "op" : "replace",
         "path" : "/contentHandling",
         "value" : "CONVERT_TO_TEXT"
       },
       {
         "op" : "add",
         "path" : "/requestTemplates/application~1octet-stream",
         "value" : "{\"body\": \"$input.body\"}"
       }
     ]
   }
   ```

## Convert text data to a binary payload<a name="api-gateway-payload-encodings-convert-string-to-binary"></a>

Suppose a Lambda function returns an image file as a base64\-encoded string\. To pass this binary output to the client through API Gateway, do the following: 

1. Update the API's `binaryMediaTypes` list by adding the binary media type of `application/octet-stream`, if it is not already in the list\. 

   ```
   PATCH /restapis/<restapi_id>
   
   {
     "patchOperations" : [ {
       "op" : "add",
       "path" : "/binaryMediaTypes/application~1octet-stream",
     }]
   }
   ```

1.  Set the `contentHandling` property on the `Integration` resource to `CONVERT_TO_BINARY`\. Do not define a mapping template\. If you don't define a mapping template, API Gateway invokes the passthrough template to return the base64\-decoded binary blob as the image file to the client\. 

   ```
   PATCH /restapis/<restapi_id>/resources/<resource_id>/methods/<http_method>/integration/responses/<status_code>
   
   {
     "patchOperations" : [
       {
         "op" : "replace",
         "path" : "/contentHandling",
         "value" : "CONVERT_TO_BINARY"
       }
     ]
   }
   ```

## Pass through a binary payload<a name="api-gateway-payload-encodings-pass-binary-as-is"></a>

 To store an image in an Amazon S3 bucket using API Gateway, do the following: 

1. Update the API's `binaryMediaTypes` list by adding the binary media type of `application/octet-stream`, if it isn't already in the list\. 

   ```
   PATCH /restapis/<restapi_id>
   
   {
     "patchOperations" : [ {
       "op" : "add",
       "path" : "/binaryMediaTypes/application~1octet-stream"
     }
    ]
   }
   ```

1. On the `contentHandling` property of the `Integration` resource, set `CONVERT_TO_BINARY`\. Set `WHEN_NO_MATCH` as the `passthroughBehavior` property value without defining a mapping template\. This enables API Gateway to invoke the passthrough template\. 

   ```
   PATCH /restapis/<restapi_id>/resources/<resource_id>/methods/<http_method>/integration
   
   {
     "patchOperations" : [
       {
         "op" : "replace",
         "path" : "/contentHandling",
         "value" : "CONVERT_TO_BINARY"
       },
       {
         "op" : "replace",
         "path" : "/passthroughBehaviors",
         "value" : "WHEN_NO_MATCH"
       }
     ]
   }
   ```