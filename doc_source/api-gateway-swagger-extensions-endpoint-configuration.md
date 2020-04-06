# x\-amazon\-apigateway\-endpoint\-configuration object<a name="api-gateway-swagger-extensions-endpoint-configuration"></a>

Specifies details of the endpoint configuration used by this REST API\. This extension is an extended property of the [OpenAPI Operation](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md#operationObject) object\. This object should be present in [top\-level vendor extensions](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#vendorExtensions) for Swagger 2\.0\. For OpenAPI 3\.0, it should be present under the vendor extensions of the [Server object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md#serverObject)\.


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| vpcEndpointIds | An array of String |  A list of VpcEndpoint identifiers against which to create Route 53 alias records for a REST API\. It is only supported for the `PRIVATE` endpoint type\.  | 

## x\-amazon\-apigateway\-endpoint\-configuration example<a name="api-gateway-swagger-extensions-endpoint-configuration-example"></a>

The following example associates specified VPC endpoints to the REST API\.

```
"x-amazon-apigateway-endpoint-configuration": {
    "vpcEndpointIds": ["vpce-0212a4ababd5b8c3e", "vpce-01d622316a7df47f9"]
}
```