# Import an OpenAPI file to update an existing API definition<a name="api-gateway-import-api-update"></a>

 You can import API definitions only to update an existing API, without changing its endpoint configuration, as well as stages and stage variables, or references to API keys\. 

 The import\-to\-update operation can occur in two modes: merge or overwrite\. 

When an API \(`A`\) is merged into another \(`B`\), the resulting API retains the definitions of both `A` and `B` if the two APIs do not share any conflicting definitions\. When conflicts arise, the method definitions of the merging API \(`A`\) overrides the corresponding method definitions of the merged API \(`B`\)\. For example, suppose `B` has declared the following methods to return `200` and `206` responses:

```
GET /a
POST /a
```

and `A` declares the following method to return `200` and `400` responses:

```
GET /a
```

When `A` is merged into `B`, the resulting API yields the following methods:

```
GET /a
```

which returns `200` and `400` responses, and 

```
POST /a
```

which returns `200` and `206` responses\.

Merging an API is useful when you have decomposed your external API definitions into multiple, smaller parts and only want to apply changes from one of those parts at a time\. For example, this might occur if multiple teams are responsible for different parts of an API and have changes available at different rates\. In this mode, items from the existing API that aren't specifically defined in the imported definition are left alone\. 

When an API \(`A`\) overwrites another API \(`B`\), the resulting API takes the definitions of the overwriting API \(`A`\)\. Overwriting an API is useful when an external API definition contains the complete definition of an API\. In this mode, items from an existing API that aren't specifically defined in the imported definition are deleted\. 

 To merge an API, submit a `PUT` request to `https://apigateway.<region>.amazonaws.com/restapis/<restapi_id>?mode=merge`\. The `restapi_id` path parameter value specifies the API to which the supplied API definition will be merged\. 

 The following code snippet shows an example of the `PUT` request to merge an OpenAPI API definition in JSON, as the payload, with the specified API already in API Gateway\. 

```
PUT /restapis/<restapi_id>?mode=merge
Host:apigateway.<region>.amazonaws.com
Content-Type: application/json
Content-Length: ...

[An OpenAPI API definition in JSON](api-as-lambda-proxy-export-swagger-with-extensions.md)
```

 The merging update operation takes two complete API definitions and merges them together\. For a small and incremental change, you can use the [resource update](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-update/) operation\. 

 To overwrite an API, submit a `PUT` request to `https://apigateway.<region>.amazonaws.com/restapis/<restapi_id>?mode=overwrite`\. The `restapi_id` path parameter specifies the API that will be overwritten with the supplied API definitions\. 

 The following code snippet shows an example of an overwriting request with the payload of a JSON\-formatted OpenAPI definition: 

```
PUT /restapis/<restapi_id>?mode=overwrite
Host:apigateway.<region>.amazonaws.com
Content-Type: application/json
Content-Length: ...

[An OpenAPI API definition in JSON](api-as-lambda-proxy-export-swagger-with-extensions.md)
```

 When the `mode` query parameter isn't specified, merge is assumed\.

**Note**  
 The `PUT` operations are idempotent, but not atomic\. That means if a system error occurs part way through processing, the API can end up in a bad state\. However, repeating the operation puts the API into the same final state as if the first operation had succeeded\.  