# Updates to a REST API that require redeployment<a name="updating-api"></a>

Maintaining an API amounts to viewing, updating and deleting the existing API setups\. You can maintain an API using the API Gateway console, AWS CLI, an SDK or the API Gateway REST API\. Updating an API involves modifying certain resource properties or configuration settings of the API\. Resource updates require redeploying the API, whereas configuration updates do not\. 

API resources that can be updated are detailed in the following table\.


**API resource updates requiring redeployment of the API**  

| Resource | Remarks | 
| --- | --- | 
| [ApiKey](https://docs.aws.amazon.com/apigateway/latest/api/API_ApiKey.html) | For applicable properties and supported operations, see [apikey:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateApiKey.html#remarks)\. The update requires redeploying the API\. | 
| [Authorizer](https://docs.aws.amazon.com/apigateway/latest/api/API_Authorizer.html) | For applicable properties and supported operations, see [authorizer:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateAuthorizer.html#remarks)\. The update requires redeploying the API\. | 
| [DocumentationPart](https://docs.aws.amazon.com/apigateway/latest/api/API_DocumentationPart.html) | For applicable properties and supported operations, see [documentationpart:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateDocumentationPart.html#remarks)\. The update requires redeploying the API\. | 
| [DocumentationVersion](https://docs.aws.amazon.com/apigateway/latest/api/API_DocumentationVersion.html) | For applicable properties and supported operations, see [documentationversion:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateDocumentationVersion.html#remarks)\. The update requires redeploying the API\. | 
| [GatewayResponse](https://docs.aws.amazon.com/apigateway/latest/api/API_GatewayResponse.html) | For applicable properties and supported operations, see [gatewayresponse:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateGatewayResponse.html#remarks)\. The update requires redeploying the API\. | 
| [Integration](https://docs.aws.amazon.com/apigateway/latest/api/API_Integration.html) |  For applicable properties and supported operations, see [integration:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateIntegration.html#remarks)\. The update requires redeploying the API\.  | 
| [IntegrationResponse](https://docs.aws.amazon.com/apigateway/latest/api/API_IntegrationResponse.html) | For applicable properties and supported operations, see [integrationresponse:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateIntegrationResponse.html#remarks)\. The update requires redeploying the API\. | 
| [Method](https://docs.aws.amazon.com/apigateway/latest/api/API_Method.html) | For applicable properties and supported operations, see [method:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateMethod.html#remarks)\. The update requires redeploying the API\. | 
| [MethodResponse](https://docs.aws.amazon.com/apigateway/latest/api/API_MethodResponse.html) | For applicable properties and supported operations, see [methodresponse:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateMethodResponse.html#remarks)\. The update requires redeploying the API\. | 
| [Model](https://docs.aws.amazon.com/apigateway/latest/api/API_Model.html) | For applicable properties and supported operations, see [model:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateModel.html#remarks)\. The update requires redeploying the API\. | 
| [RequestValidator](https://docs.aws.amazon.com/apigateway/latest/api/API_RequestValidator.html) | For applicable properties and supported operations, see [requestvalidator:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateRequestValidator.html#remarks)\. The update requires redeploying the API\. | 
| [Resource](https://docs.aws.amazon.com/apigateway/latest/api/API_Resource.html) | For applicable properties and supported operations, see [resource:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateResource.html#remarks)\. The update requires redeploying the API\. | 
| [RestApi](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateRestApi.html#remarks) | For applicable properties and supported operations, see [restapi:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateRestApi.html#remarks)\. The update requires redeploying the API\. | 
| [VpcLink](https://docs.aws.amazon.com/apigateway/latest/api/API_VpcLink.html) | For applicable properties and supported operations, see [vpclink:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateVpcLink.html#remarks)\. The update requires redeploying the API\. | 

 API configurations that can be updated are detailed in the following table\.


**API configuration updates without requiring redeployment of the API**  

| Configuration | Remarks | 
| --- | --- | 
| [Account](https://docs.aws.amazon.com/apigateway/latest/api/API_GetAccount.html) |  For applicable properties and supported operations, see [account:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateAccount.html#remarks)\. The update does not require redeploying the API\.  | 
| [Deployment](https://docs.aws.amazon.com/apigateway/latest/api/API_Deployment.html) | For applicable properties and supported operations, see [deployment:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateDeployment.html#remarks)\.  | 
| [DomainName](https://docs.aws.amazon.com/apigateway/latest/api/API_DomainName.html) | For applicable properties and supported operations, see [domainname:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateDomainName.html#remarks)\. The update does not require redeploying the API\. | 
| [BasePathMapping](https://docs.aws.amazon.com/apigateway/latest/api/API_BasePathMapping.html) |  For applicable properties and supported operations, see [basepathmapping:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateBasePathMapping.html#remarks)\. The update does not require redeploying the API\.  | 
| [Stage](https://docs.aws.amazon.com/apigateway/latest/api/API_Stage.html) |  For applicable properties and supported operations, see [stage:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateStage.html#remarks)\. The update does not require redeploying the API\.  | 
| [Usage](https://docs.aws.amazon.com/apigateway/latest/api/API_GetUsage.html) |  For applicable properties and supported operations, see [usage:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateUsage.html#remarks)\. The update does not require redeploying the API\.  | 
| [UsagePlan](https://docs.aws.amazon.com/apigateway/latest/api/API_UsagePlan.html) | For applicable properties and supported operations, see [usageplan:update](https://docs.aws.amazon.com/apigateway/latest/api/API_UpdateUsagePlan.html#remarks)\. The update does not require redeploying the API\. | 

**Topics**