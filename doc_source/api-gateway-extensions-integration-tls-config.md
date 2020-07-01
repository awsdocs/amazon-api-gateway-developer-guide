# x\-amazon\-apigateway\-integration\.tlsConfig object<a name="api-gateway-extensions-integration-tls-config"></a>

Specifies the TLS configuration for an integration\.


**Properties**  

| Property name | Type | Description | 
| --- | --- | --- | 
| insecureSkipVerification | Boolean |  Supported only for REST APIs\. Specifies whether or not API Gateway skips verification that the certificate for an integration endpoint is issued by a [supported certificate authority](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-supported-certificate-authorities-for-http-endpoints.html)\. This isn’t recommended, but it enables you to use certificates that are signed by private certificate authorities, or certificates that are self\-signed\. If enabled, API Gateway still performs basic certificate validation, which includes checking the certificate's expiration date, hostname, and presence of a root certificate authority\. Supported only for `HTTP` and `HTTP_PROXY` integrations\.  | 
| serverNameToVerify | string |  Supported only for HTTP API private integrations\. If you specify a server name, API Gateway uses it to verify the hostname on the integration's certificate\. The server name is also included in the TLS handshake to support Server Name Indication \(SNI\) or virtual hosting\.  | 

## x\-amazon\-apigateway\-integration\.tlsConfig examples<a name="api-gateway-extensions-integration-tls-config-example"></a>

The following OpenAPI 3\.0 example enables `insecureSkipVerification` for a REST API HTTP proxy integration\.

```
"x-amazon-apigateway-integration": {
  "uri": "http://petstore-demo-endpoint.execute-api.com/petstore/pets",
  "responses": {
     default": {
       "statusCode": "200"
      }
  },
  "passthroughBehavior": "when_no_match",
  "httpMethod": "ANY",
  "tlsConfig" : {
    "insecureSkipVerification" : true
  }
  "type": "http_proxy",
}
```

The following OpenAPI 3\.0 example specifies a `serverNameToVerify` for an HTTP API private integration\.

```
"x-amazon-apigateway-integration" : {
  "payloadFormatVersion" : "1.0",
  "connectionId" : "abc123",
  "type" : "http_proxy",
  "httpMethod" : "ANY",
  "uri" : "arn:aws:elasticloadbalancing:us-west-2:123456789012:listener/app/my-load-balancer/50dc6c495c0c9188/0467ef3c8400ae65",
  "connectionType" : "VPC_LINK",
  "tlsConfig" : {
     "serverNameToVerify" : "example.com"
  }
}
```