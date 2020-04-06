# Integration passthrough behaviors<a name="integration-passthrough-behaviors"></a>

 With non\-proxy integrations, when a method request carries a payload and either the `Content-Type` header does not match any specified mapping template or no mapping template is defined, you can choose to pass the client\-supplied request payload through the integration request to the backend without transformation\. The process is known as integration passthrough\. 

 For [proxy integrations](api-gateway-set-up-simple-proxy.md), API Gateway passes the entire request through to your backend, and you do not have the option to modify the passthrough behaviors\. 

 The actual passthrough behavior of an incoming request is determined by the option you choose for a specified mapping template, during [integration request set\-up](how-to-method-settings-execution-console.md), and the Content Type header that a client set in the incoming request\. The following examples illustrate the possible passthrough behaviors\. 

Example 1: One mapping template is defined in the integration request for the `application/json` content type\.


| Content\-type header\\Selected passthrough option | `WHEN_NO_MATCH` | `WHEN_NO_TEMPLATES` | `NEVER` | 
| --- | --- | --- | --- | 
| None \(default to application/json | The request payload is transformed using the template\. | The request payload is transformed using the template\. | The request payload is transformed using the template\. | 
| application/json | The request payload is transformed using the template\. | The request payload is transformed using the template\. | The request payload is transformed using the template\. | 
| application/xml | The request payload is not transformed and is sent to the backend as\-is\. | The request is rejected with an HTTP 415 Unsupported Media Type response\. | The request is rejected with an HTTP 415 Unsupported Media Type response\. | 

Example 2: One mapping template is defined in the integration request for the `application/xml` content type\.


| Content\-type header\\Selected passthrough option | `WHEN_NO_MATCH` | `WHEN_NO_TEMPLATES` | `NEVER` | 
| --- | --- | --- | --- | 
| None \(default to application/json | The request payload is not transformed and is sent to the backend as\-is\. | The request is rejected with an HTTP 415 Unsupported Media Type response\. | The request is rejected with an HTTP 415 Unsupported Media Type response\. | 
| application/json | The request payload is not transformed and is sent to the backend as\-is\. | The request is rejected with an HTTP 415 Unsupported Media Type response\. | The request is rejected with an HTTP 415 Unsupported Media Type response\. | 
| application/xml | The request payload is transformed using the template\. | The request payload is transformed using the template\. | The request payload is transformed using the template\. | 