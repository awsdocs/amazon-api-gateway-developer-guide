# Customizing HTTP API access logs<a name="http-api-logging-variables"></a>

## <a name="http-api-logging-variables.table"></a>

You can use the following variables to customize HTTP API access logs\. To learn more about access logs for HTTP APIs, see [Configuring logging for an HTTP API](http-api-logging.md)\.


| Parameter | Description | 
| --- | --- | 
| $context\.accountId |  The API owner's AWS account ID\.  | 
| $context\.apiId |  The identifier API Gateway assigns to your API\.  | 
| $context\.authorizer\.claims\.property |  A property of the claims returned from the JSON Web Token \(JWT\) after the method caller is successfully authenticated\. For more information, see [Controlling access to HTTP APIs with JWT authorizers](http-api-jwt-authorizer.md)\.  Calling `$context.authorizer.claims` returns null\.   | 
| $context\.authorizer\.error | The error message returned from an authorizer\. | 
| $context\.authorizer\.latency | The authorizer latency in ms\. | 
| $context\.authorizer\.principalId |  The principal user identification that a Lambda authorizer returns\.  | 
| $context\.authorizer\.property |  The value of the specified key\-value pair of the `context` map returned from an API Gateway Lambda authorizer function\. For example, if the authorizer returns the following `context` map:  <pre>"context" : {<br />  "key": "value",<br />  "numKey": 1,<br />  "boolKey": true<br />}</pre> calling `$context.authorizer.key` returns the `"value"` string, calling `$context.authorizer.numKey` returns the `1`, and calling `$context.authorizer.boolKey` returns `true`\.  | 
| $context\.authorizer\.status | The status code returned from an authorizer\. | 
| $context\.awsEndpointRequestId |  The AWS endpoint's request ID from the `x-amz-request-id` or `x-amzn-requestId` header\.  | 
| $context\.awsEndpointRequestId2 |  The AWS endpoint's request ID from the `x-amz-id-2` header\.  | 
| $context\.dataProcessed | The amount of data processed in bytes\. | 
| $context\.domainName |  The full domain name used to invoke the API\. This should be the same as the incoming `Host` header\.  | 
| $context\.domainPrefix |  The first label of the `$context.domainName`\. This is often used as a caller/customer identifier\.  | 
| $context\.error\.message |  A string that contains an API Gateway error message\.  | 
| $context\.error\.messageString | The quoted value of $context\.error\.message, namely "$context\.error\.message"\. | 
| $context\.error\.responseType |  A type of `GatewayResponse`\. For more information, see [Monitoring WebSocket API execution with CloudWatch metrics](apigateway-websocket-api-logging.md) and [Setting up gateway responses to customize error responses](api-gateway-gatewayResponse-definition.md#customize-gateway-responses)\.  | 
| $context\.extendedRequestId | The extended ID that API Gateway assigns to the API request, which contains more useful information for debugging/troubleshooting\. | 
| $context\.httpMethod |  The HTTP method used\. Valid values include: `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, and `PUT`\.  | 
| $context\.identity\.accountId |  The AWS account ID associated with the request\. Supported for routes that use IAM authorization\.  | 
| $context\.identity\.caller |  The principal identifier of the caller making the request\. Supported for routes that use IAM authorization\.  | 
| $context\.identity\.cognitoAuthenticationProvider |  A comma\-separated list of the Amazon Cognito authentication providers used by the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\. For information, see [Using Federated Identities](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html) in the *Amazon Cognito Developer Guide*\.  | 
| $context\.identity\.cognitoAuthenticationType |  The Amazon Cognito authentication type of the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\.  | 
| $context\.identity\.cognitoIdentityId |  The Amazon Cognito identity ID of the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\.  | 
| $context\.identity\.cognitoIdentityPoolId |  The Amazon Cognito identity pool ID of the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\.  | 
| $context\.identity\.principalOrgId |  The [AWS organization ID](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_org_details.html)\. Supported for routes that use IAM authorization\.  | 
| $context\.identity\.clientCert\.clientCertPem |  The PEM\-encoded client certificate that the client presented during mutual TLS authentication\. Present when a client accesses an API by using a custom domain name that has mutual TLS enabled\.  | 
| $context\.identity\.clientCert\.subjectDN |  The distinguished name of the subject of the certificate that a client presents\. Present when a client accesses an API by using a custom domain name that has mutual TLS enabled\.  | 
| $context\.identity\.clientCert\.issuerDN |  The distinguished name of the issuer of the certificate that a client presents\. Present when a client accesses an API by using a custom domain name that has mutual TLS enabled\.  | 
| $context\.identity\.clientCert\.serialNumber |  The serial number of the certificate\. Present when a client accesses an API by using a custom domain name that has mutual TLS enabled\.  | 
| $context\.identity\.clientCert\.validity\.notBefore |  The date before which the certificate is invalid\. Present when a client accesses an API by using a custom domain name that has mutual TLS enabled\.  | 
| $context\.identity\.clientCert\.validity\.notAfter |  The date after which the certificate is invalid\. Present when a client accesses an API by using a custom domain name that has mutual TLS enabled\.  | 
| $context\.identity\.sourceIp |  The source IP address of the TCP connection making the request to API Gateway\.  You should not trust this value if there is any chance that the `X-Forwarded-For` header could be forged\.   | 
| $context\.identity\.user |  The principal identifier of the user making the request\. Supported for routes that use IAM authorization\.  | 
| $context\.identity\.userAgent |  The [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) header of the API caller\.  | 
| $context\.identity\.userArn |  The Amazon Resource Name \(ARN\) of the effective user identified after authentication\. Supported for routes that use IAM authorization\. For more information, see [https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html)\.  | 
| $context\.integration\.error | The error message returned from an integration\. Equivalent to $context\.integrationErrorMessage\. | 
| $context\.integration\.integrationStatus | For Lambda proxy integration, the status code returned from AWS Lambda, not from the backend Lambda function code\. | 
| $context\.integration\.latency | The integration latency in ms\. Equivalent to $context\.integrationLatency\. | 
| $context\.integration\.requestId | The AWS endpoint's request ID\. Equivalent to $context\.awsEndpointRequestId\. | 
| $context\.integration\.status | The status code returned from an integration\. For Lambda proxy integrations, this is the status code that your Lambda function code returns\. Equivalent to $context\.integrationStatus\. | 
| $context\.integrationErrorMessage |  A string that contains an integration error message\.  | 
| $context\.integrationLatency | The integration latency in ms\. | 
| $context\.integrationStatus | For Lambda proxy integration, this parameter represents the status code returned from AWS Lambda, not from the backend Lambda function\. | 
| $context\.path | The request path\. For example, /\{stage\}/root/child\.  | 
| $context\.protocol | The request protocol, for example, HTTP/1\.1\. | 
| $context\.requestId |  The ID that API Gateway assigns to the API request\.  | 
| $context\.requestTime | The [CLF](https://httpd.apache.org/docs/1.3/logs.html#common)\-formatted request time \(dd/MMM/yyyy:HH:mm:ss \+\-hhmm\)\. | 
| $context\.requestTimeEpoch | The [Epoch](https://en.wikipedia.org/wiki/Unix_time)\-formatted request time\. | 
| $context\.responseLatency | The response latency in ms\. | 
| $context\.responseLength | The response payload length\. | 
| $context\.routeKey |  The route key of the API request, for example `/pets`\.  | 
| $context\.stage |  The deployment stage of the API request \(for example, `beta` or `prod`\)\.  | 
| $context\.status | The method response status\. | 