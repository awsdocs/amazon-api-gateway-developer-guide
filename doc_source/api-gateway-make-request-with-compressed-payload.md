# Call an API method with a compressed payload<a name="api-gateway-make-request-with-compressed-payload"></a>

To make an API request with a compressed payload, the client must set the `Content-Encoding` header with one of the [supported content codings](api-gateway-enable-compression.md#api-gateway-supported-content-encodings)\. 

Suppose that you're an API client and want to call the PetStore API method \(`POST /pets`\)\. Don't call the method by using the following JSON output:

```
POST /pets
Host: {petstore-api-id}.execute-api.{region}.amazonaws.com
Content-Length: ...

{
  "type": "dog",
  "price": 249.99
}
```

Instead, you can call the method with the same payload compressed by using the GZIP coding:

```
POST /pets
Host: {petstore-api-id}.execute-api.{region}.amazonaws.com
Content-Encoding:gzip
Content-Length: ...

���RPP*�,HU�RPJ�OW��e&���L,�,-y�j
```

When API Gateway receives the request, it verifies if the specified content coding is supported\. Then, it attempts to decompress the payload with the specified content coding\. If the decompression is successful, it dispatches the request to the integration endpoint\. If the specified coding isn't supported or the supplied payload isn't compressed with specified coding, API Gateway returns the `415 Unsupported Media Type` error response\. The error is not logged to CloudWatch Logs, if it occurs in the early phase of decompression before your API and stage are identified\.  