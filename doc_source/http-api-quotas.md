# HTTP API Quotas<a name="http-api-quotas"></a>


|  | 
| --- |
| HTTP APIs are in beta for Amazon API Gateway and are subject to change\. | 

Unless noted otherwise, the following quotas can be increased upon request\. To request an increase, you can contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

## <a name="http-api-quotas.table"></a>


| Resource or Operation | Default Quota | Can Be Increased | 
| --- | --- | --- | 
| Requests per second | 10,000 | Yes | 
| Regional APIs per account | 600 | No | 
| Routes per API  | 300 | Yes | 
| Integrations per API | 300 | No | 
| Maximum integration timeout | 30 seconds | No | 
| Stages per API | 10 | Yes | 
| Tags per stage  | 50 | No | 
| Total combined size of request line and header values | 4096 bytes | No | 
| Payload size | 6 MB | No | 
| Custom domains per account | 120 | Yes | 
| Access log template size  | 3 KB | No | 
| Amazon CloudWatch Logs log entry  | 1 MB | No | 
| Authorizers per API  | 10 | Yes | 
| Audiences per authorizer  | 50 | No | 
| Scopes per route  | 10 | No | 
| Timeout for JSON Web Key Set endpoint  | 1500 ms | No | 
| Timeout for OpenID Connect discovery endpoint  | 1500 ms | No | 