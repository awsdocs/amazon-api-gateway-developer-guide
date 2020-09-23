# Choosing between HTTP APIs and REST APIs<a name="http-api-vs-rest"></a>

## <a name="http-api-vs-rest.differences"></a>

HTTP APIs are designed for low\-latency, cost\-effective AWS Lambda proxy and HTTP proxy APIs\. HTTP APIs support OIDC and OAuth 2\.0 authorization, and come with built\-in support for CORS and automatic deployments\. Previous\-generation REST APIs currently offer more features, and full control over API requests and responses\.

The following tables summarize core features that are available in HTTP APIs and REST APIs\.


| Authorizers | HTTP API | REST API | 
| --- | --- | --- | 
|  AWS Lambda  |   |  ✓  | 
|  IAM  |   |  ✓  | 
|  Amazon Cognito  |  ✓ \*  |  ✓  | 
|  Native OpenID Connect / OAuth 2\.0  |  ✓  |   | 

\* You can use Amazon Cognito as a JWT issuer\.


| Integration | HTTP API | REST API | 
| --- | --- | --- | 
|  HTTP proxy  |  ✓  |  ✓  | 
|  Lambda proxy  |  ✓  |  ✓  | 
|  HTTP  |   |  ✓  | 
|  AWS services  |  ✓  |  ✓  | 
|  Private integration  |  ✓  |  ✓  | 
|  Mock  |   |  ✓  | 


| API management | HTTP API | REST API | 
| --- | --- | --- | 
|  Usage plans  |   |  ✓  | 
|  API keys  |   |  ✓  | 
|  Custom domain names  |  ✓ \*  |  ✓  | 

\* HTTP APIs don't support TLS 1\.0\.


| Development | HTTP API | REST API | 
| --- | --- | --- | 
|  Cache  |   |  ✓  | 
|  Request transformation  |   |  ✓  | 
|  Request / response validation  |   |  ✓  | 
|  Test invocation  |   |  ✓  | 
|  CORS configuration  |  ✓  |   | 
|  Automatic deployments  |  ✓  |   | 
|  Default stage  |  ✓  |   | 
|  Default route  |  ✓  |   | 


| Security | HTTP API | REST API | 
| --- | --- | --- | 
|  Client certificates  |   |  ✓  | 
|  AWS WAF  |   |  ✓  | 
|  Resource policies  |   |  ✓  | 


| API type | HTTP API | REST API | 
| --- | --- | --- | 
|  Regional  |  ✓  |  ✓  | 
|  Edge\-optimized  |   |  ✓  | 
|  Private  |   |  ✓  | 


| Monitoring | HTTP API | REST API | 
| --- | --- | --- | 
|  Access logs to Amazon CloudWatch Logs  |  ✓  |  ✓  | 
|  Access logs to Amazon Kinesis Data Firehose  |   |  ✓  | 
|  Execution logs  |   |  ✓  | 
|  Amazon CloudWatch metrics  |  ✓  |  ✓  | 
|  AWS X\-Ray  |   |  ✓  | 