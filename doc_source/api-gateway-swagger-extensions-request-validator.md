# x\-amazon\-apigateway\-request\-validator property<a name="api-gateway-swagger-extensions-request-validator"></a>

 Specifies a request validator, by referencing a `request_validator_name` of the [x\-amazon\-apigateway\-request\-validators object](api-gateway-swagger-extensions-request-validators.md) map, to enable request validation on the containing API or a method\. The value of this extension is a JSON string\.

This extension can be specified at the API level or at the method level\. The API\-level validator applies to all of the methods unless it is overridden by the method\-level validator\. 

## `x-amazon-apigateway-request-validator` example<a name="api-gateway-swagger-extensions-request-validator-example"></a>

The following example applies the `basic` request validator at the API level while applying the `parameter-only` request validator on the `POST /validation` request\. 

------
#### [ OpenAPI 2\.0 ]

```
{
  "swagger": "2.0",
  "x-amazon-apigateway-request-validators" : {
    "basic" : {
      "validateRequestBody" : true,
      "validateRequestParameters" : true
    },
    "params-only" : {
      "validateRequestBody" : false,
      "validateRequestParameters" : true
    }
  },
  "x-amazon-apigateway-request-validator" : "basic",
  "paths": {
    "/validation": {
      "post": {
        "x-amazon-apigateway-request-validator" : "params-only",
       ...
     }
}
```

------