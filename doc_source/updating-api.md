# Update and Maintain an API in Amazon API Gateway<a name="updating-api"></a>

Maintaining an API amounts to viewing, updating and deleting the existing API setups\. You can maintain an API using the API Gateway console, AWS CLI, an SDK or the API Gateway REST API\. Updating an API involves modifying certain resource properties or configuration settings of the API\. Resource updates require redeploying the API, whereas configuration updates do not\. 

API resources that can be updated are detailed in the following table\.


**API resource updates requiring redeployment of the API**  

| Resource | Remarks | 
| --- | --- | 
| [ApiKey](http://docs.aws.amazon.com/apigateway/api-reference/resource/api-key/) | For applicable properties and supported operations, see [apikey:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-update/#remarks)\. The update requires redeploying the API\. | 
| [Authorizer](http://docs.aws.amazon.com/apigateway/api-reference/resource/authorizer/) | For applicable properties and supported operations, see [authorizer:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/authorizer-update/#remarks)\. The update requires redeploying the API\. | 
| [DocumentationPart](http://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/) | For applicable properties and supported operations, see [documentationpart:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/documentationpart-update/#remarks)\. The update requires redeploying the API\. | 
| [DocumentationVersion](http://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-version/) | For applicable properties and supported operations, see [documentationversion:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/documentationversion-update/#remarks)\. The update requires redeploying the API\. | 
| [GatewayResponse](http://docs.aws.amazon.com/apigateway/api-reference/resource/gateway-response/) | For applicable properties and supported operations, see [gatewayresponse:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/gatewayresponse-update/#remarks)\. The update requires redeploying the API\. | 
| [Integration](http://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) |  For applicable properties and supported operations, see [integration:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/integration-update/#remarks)\. The update requires redeploying the API\.  | 
| [IntegrationResponse](http://docs.aws.amazon.com/apigateway/api-reference/resource/integration-response/) | For applicable properties and supported operations, see [integrationresponse:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/integrationresponse-update/#remarks)\. The update requires redeploying the API\. | 
| [Method](http://docs.aws.amazon.com/apigateway/api-reference/resource/method/) | For applicable properties and supported operations, see [method:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-update/#remarks)\. The update requires redeploying the API\. | 
| [MethodResponse](http://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/) | For applicable properties and supported operations, see [methodresponse:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/methodresponse-update/#remarks)\. The update requires redeploying the API\. | 
| [Model](http://docs.aws.amazon.com/apigateway/api-reference/resource/model/) | For applicable properties and supported operations, see [model:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/model-update/#remarks)\. The update requires redeploying the API\. | 
| [RequestValidator](http://docs.aws.amazon.com/apigateway/api-reference/resource/request-validator/) | For applicable properties and supported operations, see [requestvalidator:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/requestvalidator-update/#remarks)\. The update requires redeploying the API\. | 
| [Resource](http://docs.aws.amazon.com/apigateway/api-reference/resource/resource/) | For applicable properties and supported operations, see [resource:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/resource-update/#remarks)\. The update requires redeploying the API\. | 
| [RestApi](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/#remarks) | For applicable properties and supported operations, see [restapi:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/#remarks)\. The update requires redeploying the API\. | 
| [VpcLink](http://docs.aws.amazon.com/apigateway/api-reference/resource/vpc-link/) | For applicable properties and supported operations, see [vpclink:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/vpclink-update/#remarks)\. The update requires redeploying the API\. | 

 API configurations that can be updated are detailed in the following table\.


**API configuration updates without requiring redeployment of the API**  

| Configuration | Remarks | 
| --- | --- | 
| [Account](http://docs.aws.amazon.com/apigateway/api-reference/resource/account/) |  For applicable properties and supported operations, see [account:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/account-update/#remarks)\. The update does not require redeploying the API\.  | 
| [Deployment](http://docs.aws.amazon.com/apigateway/api-reference/resource/deployment/) | For applicable properties and supported operations, see [deployment:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-update/#remarks)\.  | 
| [DomainName](http://docs.aws.amazon.com/apigateway/api-reference/resource/domain-name/) | For applicable properties and supported operations, see [domainname:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-update/#remarks)\. The update does not require redeploying the API\. | 
| [BasePathMapping](http://docs.aws.amazon.com/apigateway/api-reference/resource/base-path-mapping/) |  For applicable properties and supported operations, see [basepathmapping:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/basepathmapping-update/#remarks)\. The update does not require redeploying the API\.  | 
| [Stage](http://docs.aws.amazon.com/apigateway/api-reference/resource/stage/) |  For applicable properties and supported operations, see [stage:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/#remarks)\. The update does not require redeploying the API\.  | 
| [Usage](http://docs.aws.amazon.com/apigateway/api-reference/resource/usage/) |  For applicable properties and supported operations, see [usage:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/usage-update/#remarks)\. The update does not require redeploying the API\.  | 
| [UsagePlan](http://docs.aws.amazon.com/apigateway/api-reference/resource/usage-plan/) | For applicable properties and supported operations, see [usageplan:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/usageplan-update/#remarks)\. The update does not require redeploying the API\. | 

**Topics**
+ [Update an API Endpoint Type in API Gateway](apigateway-api-migration.md)
+ [Maintain an API Using the API Gateway Console](maintain-api-using-console.md)