# x\-amazon\-apigateway\-request\-validators object<a name="api-gateway-swagger-extensions-request-validators"></a>

 Defines the supported request validators for the containing API as a map between a validator name and the associated request validation rules\. This extension applies to an API\.


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| `request_validator_name` | [x\-amazon\-apigateway\-request\-validators\.requestValidator object](api-gateway-swagger-extensions-request-validators.requestValidator.md) |  Specifies the validation rules consisting of the named validator\. For example:  <pre>    "basic" : {<br />      "validateRequestBody" : true,<br />      "validateRequestParameters" : true<br />    },<br /></pre> To apply this validator to a specific method, reference the validator name \(`basic`\) as the value of the [x\-amazon\-apigateway\-request\-validator property](api-gateway-swagger-extensions-request-validator.md) property\.  | 

## `x-amazon-apigateway-request-validators` example<a name="api-gateway-swagger-extensions-request-validators-example"></a>

 The following example shows a set of request validators for an API as a map between a validator name and the associated request validation rules\.

------
#### [ OpenAPI 2\.0 ]

```
{
  "swagger": "2.0",
  ...
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
  ...
}
```

------