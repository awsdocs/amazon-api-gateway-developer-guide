# Representation of API documentation in API Gateway<a name="api-gateway-documenting-api-content-representation"></a>

API Gateway API documentation consists of individual documentation parts associated with specific API entities that include API, resource, method, request, response, message parameters \(i\.e\., path, query, header\), as well as authorizers and models\. 

In API Gateway, a documentation part is represented by a [DocumentationPart](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/) resource\. The API documentation as a whole is represented by the [DocumentationParts](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-parts) collection\. 

Documenting an API involves creating `DocumentationPart` instances, adding them to the `DocumentationParts` collection, and maintaining versions of the documentation parts as your API evolves\.

**Topics**
+ [Documentation parts](#api-gateway-documenting-api-content-representation-documentation-parts)
+ [Documentation versions](#api-gateway-documenting-api-content-representation-documentation-versions)

## Documentation parts<a name="api-gateway-documenting-api-content-representation-documentation-parts"></a>

A [DocumentationPart](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/) resource is a JSON object that stores the documentation content applicable to an individual API entity\. Its `properties` field contains the documentation content as a map of key\-value pairs\. Its `location` property identifies the associated API entity\. 

The shape of a content map is determined by you, the API developer\. The value of a key\-value pair can be a string, number, boolean, object, or array\. The shape of the `location` object depends on the targeted entity type\. 

The `DocumentationPart` resource supports content inheritance: the documentation content of an API entity is applicable to children of that API entity\. For more information about the definition of child entities and content inheritance, see [Inherit Content from an API Entity of More General Specification](#api-gateway-documenting-api-content-inheritance)\. 

### Location of a documentation part<a name="api-gateway-documenting-api-content-representation-documentation-parts-target"></a>

The [location](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/#location) property of a [DocumentationPart](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part) instance identifies an API entity to which the associated content applies\. The API entity can be an API Gateway REST API resource, such as [RestApi](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/), [Resource](https://docs.aws.amazon.com/apigateway/api-reference/resource/resource/), [Method](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/), [MethodResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/), [Authorizer](https://docs.aws.amazon.com/apigateway/api-reference/resource/authorizer/), or [Model](https://docs.aws.amazon.com/apigateway/api-reference/resource/model)\. The entity can also be a message parameter, such as a URL path parameter, a query string parameter, a request or response header parameter, a request or response body, or response status code\. 

To specify an API entity, set the [type](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/#type) attribute of the `location` object to be one of `API`, `AUTHORIZER`, `MODEL`, `RESOURCE`, `METHOD`, `PATH_PARAMETER`, `QUERY_PARAMETER`, `REQUEST_HEADER`, `REQUEST_BODY`, `RESPONSE`, `RESPONSE_HEADER`, or `RESPONSE_BODY`\. 

Depending on the `type` of an API entity, you might specify other `location` attributes, including [method](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/#method), [name](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/#name), [path](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/#path), and [statusCode](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/#statusCode)\. Not all of these attributes are valid for a given API entity\. For example, `type`, `path`, `name`, and `statusCode` are valid attributes of the `RESPONSE` entity; only `type` and `path` are valid location attributes of the `RESOURCE` entity\. It is an error to include an invalid field in the `location` of a `DocumentationPart` for a given API entity\.

Not all valid `location` fields are required\. For example, `type` is both the valid and required `location` field of all API entities\. However, `method`, `path`, and `statusCode` are valid but not required attributes for the `RESPONSE` entity\. When not explicitly specified, a valid `location` field assumes its default value\. The default `path` value is `/`, i\.e\., the root resource of an API\. The default value of `method`, or `statusCode` is `*`, meaning any method, or status code values, respectively\.

### Content of a documentation part<a name="api-gateway-documenting-api-content-representation-documentation-parts-content"></a>

The `properties` value is encoded as a JSON string\. The `properties` value contains any information you choose to meet your documentation requirements\. For example, the following is a valid content map: 

```
{
  "info": {
    "description": "My first API with Amazon API Gateway."
  },
  "x-custom-info" : "My custom info, recognized by OpenAPI.",
  "my-info" : "My custom info not recognized by OpenAPI."
}
```

Although API Gateway accepts any valid JSON string as the content map, the content attributes are treated as two categories: those that can be recognized by OpenAPI and those that cannot\. In the preceding example, `info`, `description`, and `x-custom-info` are recognized by OpenAPI as a standard OpenAPI object, property, or extension\. In contrast, `my-info` is not compliant with the OpenAPI specification\. API Gateway propagates OpenAPI\-compliant content attributes into the API entity definitions from the associated `DocumentationPart` instances\. API Gateway does not propagate the non\-compliant content attributes into the API entity definitions\. 

As another example, here is `DocumentationPart` targeted for a `Resource` entity:

```
{
    "location" : {
        "type" : "RESOURCE",
        "path": "/pets"
    },
    "properties" : {
        "summary" : "The /pets resource represents a collection of pets in PetStore.",
        "description": "... a child resource under the root...",
    }
}
```

Here, both `type` and `path` are valid fields to identify the target of the `RESOURCE` type\. For the root resource \(`/`\), you can omit the `path` field\.

```
{
    "location" : {
        "type" : "RESOURCE"
    },
    "properties" : {
        "description" : "The root resource with the default path specification."
    }
}
```

This is the same as the following `DocumentationPart` instance:

```
{
    "location" : {
        "type" : "RESOURCE",
        "path": "/"
    },
    "properties" : {
        "description" : "The root resource with an explicit path specification"
    }
}
```

### Inherit content from an API entity of more general specifications<a name="api-gateway-documenting-api-content-inheritance"></a>

The default value of an optional `location` field provides a patterned description of an API entity\. Using the default value in the `location` object, you can add a general description in the `properties` map to a `DocumentationPart` instance with this type of `location` pattern\. API Gateway extracts the applicable OpenAPI documentation attributes from the `DocumentationPart` of the generic API entity and injects them into a specific API entity with the `location` fields matching the general `location` pattern, or matching the exact value, unless the specific entity already has a `DocumentationPart` instance associated with it\. This behavior is also known as content inheritance from an API entity of more general specifications\. 

Content inheritance does not apply to certain API entity types\. See the table below for details\.

When an API entity matches more than one `DocumentationPart`'s location pattern, the entity will inherit the documentation part with the location fields of the highest precedence and specificities\. The order of precedence is `path` >  `statusCode`\. For matching with the `path` field, API Gateway chooses the entity with the most specific path value\. The following table shows this with a few examples\.


| Case | `path` | `statusCode` | `name` | Remarks | 
| --- | --- | --- | --- | --- | 
| 1 | /pets | \* | id |  Documentation associated with this location pattern will be inherited by entities matching the location pattern\.  | 
| 2 | /pets | 200 | id |  Documentation associated with this location pattern will be inherited by entities matching the location pattern when both Case 1 and Case 2 are matched, because Case 2 is more specific than Case 1\.   | 
| 3 | /pets/petId | \* | id |  Documentation associated with this location pattern will be inherited by entities matching the location pattern when Cases 1, 2, and 3 are matched, because Case 3 has a higher precedence than Case 2 and is more specific than Case 1\.  | 

Here is another example to contrast a more generic `DocumentationPart` instance to a more specific one\. The following general error message of `"Invalid request error"` is injected into the OpenAPI definitions of the `400` error responses, unless overridden\. 

```
{
    "location" : {
        "type" : "RESPONSE",
        "statusCode": "400"
    },
    "properties" : {
        "description" : "Invalid request error."
    }"
}
```

With the following overwrite, the `400` responses to any methods on the `/pets` resource has a description of `"Invalid petId specified"` instead\. 

```
{
    "location" : {
        "type" : "RESPONSE",
        "path": "/pets",
        "statusCode": "400"
    },
    "properties" : "{
        "description" : "Invalid petId specified."
    }"
}
```

### Valid location fields of `DocumentationPart`<a name="api-gateway-documenting-api-content-representation-target-specification"></a>

The following table shows the valid and required fields as well as applicable default values of a [DocumentationPart](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/) resource that is associated with a given type of API entities\.


|  API entity  |  Valid location fields  | Required location fields | Default field values | Inheritable content | 
| --- | --- | --- | --- | --- | 
| [API](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/) |  <pre>{<br />    "location": {<br />        "type": "API" <br />    }, <br />    ... <br />}</pre>  | type | N/A | No | 
| [Resource](https://docs.aws.amazon.com/apigateway/api-reference/resource/resource/) |  <pre>{ <br />    "location": { <br />        "type": "RESOURCE", <br />        "path": "resource_path" <br />    }, <br />    ... <br />}</pre>  | type | The default value of path is /\.  | No | 
| [Method](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/) |  <pre>{ <br />    "location": { <br />        "type": "METHOD", <br />        "path": "resource_path", <br />        "method": "http_verb" <br />    }, <br />    ... <br />}</pre>  | type | The default values of path and method are / and \*, respectively\.  | Yes, matching path by prefix and matching method of any values\.  | 
| Query parameter |  <pre>{ <br />    "location": { <br />        "type": "QUERY_PARAMETER", <br />        "path": "resource_path", <br />        "method": "HTTP_verb",<br />        "name": "query_parameter_name" <br />    }, <br />    ... <br />}</pre>  | type | The default values of path and method are / and \*, respectively\.  | Yes, matching path by prefix and matching method by exact values\. | 
| Request body |  <pre>{ <br />    "location": { <br />        "type": "REQUEST_BODY", <br />        "path": "resource_path", <br />        "method": "http_verb" <br />    }, <br />    ... <br />}</pre>  | type | The default values of path, and method are /and \*, respectively\.  | Yes, matching path by prefix, and matching method by exact values\. | 
| Request header parameter |  <pre>{ <br />    "location": { <br />        "type": "REQUEST_HEADER", <br />        "path": "resource_path", <br />        "method": "HTTP_verb",<br />        "name": "header_name" <br />    }, <br />    ... <br />}</pre>  | type, name | The default values of path and method are / and \*, respectively\.  | Yes, matching path by prefix and matching method by exact values\. | 
| Request path parameter |  <pre>{ <br />    "location": { <br />        "type": "PATH_PARAMETER", <br />        "path": "resource/{path_parameter_name}", <br />        "method": "HTTP_verb",<br />        "name": "path_parameter_name" <br />    }, <br />    ... <br />}</pre>  | type, name | The default values of path and method are / and \*, respectively\.  | Yes, matching path by prefix and matching method by exact values\. | 
| Response |  <pre>{ <br />    "location": { <br />        "type": "RESPONSE", <br />        "path": "resource_path", <br />        "method": "http_verb", <br />        "statusCode": "status_code" <br />    }, <br />    ... <br />}</pre>  | type | The default values of path, method, and statusCode are /, \* and \*, respectively\.  | Yes, matching path by prefix and matching method and statusCode by exact values\. | 
| Response header |  <pre>{ <br />    "location": { <br />        "type": "RESPONSE_HEADER", <br />        "path": "resource_path", <br />        "method": "http_verb", <br />        "statusCode": "status_code", <br />        "name": "header_name" <br />    }, <br />    ... <br />}</pre>  | type, name | The default values of path, method and statusCode are /, \* and \*, respectively\.  | Yes, matching path by prefix and matching method, and statusCode by exact values\. | 
| Response body |  <pre>{ <br />    "location": { <br />        "type": "RESPONSE_BODY", <br />        "path": "resource_path", <br />        "method": "http_verb", <br />        "statusCode": "status_code" <br />    }, <br />    ... <br />}</pre>  | type | The default values of path, method and statusCode are /, \* and \*, respectively\.  | Yes, matching path by prefix and matching method, and statusCode by exact values\. | 
| [Authorizer](https://docs.aws.amazon.com/apigateway/api-reference/resource/authorizer/) |  <pre>{ <br />    "location": { <br />        "type": "AUTHORIZER", <br />        "name": "authorizer_name" <br />    }, <br />    ... <br />}</pre>  | type | N/A | No | 
| [Model](https://docs.aws.amazon.com/apigateway/api-reference/resource/model/) |  <pre>{ <br />    "location": { <br />        "type": "MODEL", <br />        "name": "model_name" <br />    }, <br />    ... <br />}</pre>  | type | N/A | No | 

## Documentation versions<a name="api-gateway-documenting-api-content-representation-documentation-versions"></a>

A documentation version is a snapshot of the [DocumentationParts](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-parts/) collection of an API and is tagged with a version identifier\. Publishing the documentation of an API involves creating a documentation version, associating it with an API stage, and exporting that stage\-specific version of the API documentation to an external OpenAPI file\. In API Gateway, a documentation snapshot is represented as a [DocumentationVersion](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-version) resource\. 

As you update an API, you create new versions of the API\. In API Gateway, you maintain all the documentation versions using the [DocumentationVersions](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-versions) collection\.