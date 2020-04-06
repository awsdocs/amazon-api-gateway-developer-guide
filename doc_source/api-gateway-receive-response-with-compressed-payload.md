# Receive an API response with a compressed payload<a name="api-gateway-receive-response-with-compressed-payload"></a>

When making a request on a compression\-enabled API, the client can choose to receive a compressed response payload of a specific format by specifying an `Accept-Encoding` header with a [supported content coding](api-gateway-enable-compression.md#api-gateway-supported-content-encodings)\. 

API Gateway only compresses the response payload when the following conditions are satisfied:
+  The incoming request has the `Accept-Encoding` header with a supported content coding and format\. 
**Note**  
If the header is not set, the default value is `*` as defined in [RFC 7231](https://tools.ietf.org/html/rfc7231#section-5.3.4)\. In such a case, API Gateway does not compress the payload\. Some browser or client may add `Accept-Encoding` \(for example, `Accept-Encoding:gzip, deflate, br`\) automatically to compression\-enabled requests\. This can trigger the payload compression in API Gateway\. Without an explicit specification of supported `Accept-Encoding` header values, API Gateway does not compress the payload\. 
+  The `minimumCompressionSize` is set on the API to enable compression\.
+  The integration response doesn't have a `Content-Encoding` header\. 
+  The size of an integration response payload, after the applicable mapping template is applied, is greater than or equal to the specified `minimumCompressionSize` value\.

API Gateway applies any mapping template that's configured for the integration response before compressing the payload\. If the integration response contains a `Content-Encoding` header, API Gateway assumes that the integration response payload is already compressed and skips the compression processing\.

An example is the PetStore API example and the following request:

```
GET /pets
Host: {petstore-api-id}.execute-api.{region}.amazonaws.com
Accept: application/json
```

The backend responds to the request with an uncompressed JSON payload that's similar to the following:

```
200 OK

[
  { 
    "id": 1, 
    "type": "dog", 
    "price": 249.99 
  }, 
  { 
    "id": 2, 
    "type": "cat", 
    "price": 124.99 
  }, 
  { 
    "id": 3, 
    "type": "fish", 
    "price": 0.99 
  } 
]
```

To receive this output as a compressed payload, your API client can submit a request as follows:

```
GET /pets
Host: {petstore-api-id}.execute-api.{region}.amazonaws.com
Accept-Encoding:gzip
```

The client receives the response with a `Content-Encoding` header and GZIP\-encoded payload that are similar to the following: 

```
200 OK
Content-Encoding:gzip
...

���RP�

J�)JV
�:P^IeA*������+(�L	�X�YZ�ku0L0B7!9��C#�&����Y��a���^�X
```

When the response payload is compressed, only the compressed data size is billed for data transfer\.