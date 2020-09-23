# API Gateway Amazon Resource Name \(ARN\) reference<a name="arn-format-reference"></a>

The following tables list the Amazon Resource Names \(ARNs\) for API Gateway resources\. To learn more about using ARNs in AWS Identity and Access Management policies, see [How Amazon API Gateway works with IAM](security_iam_service-with-iam.md) and [Control access to an API with IAM permissions](permissions.md)\.

## HTTP API and WebSocket API resources<a name="apigateway-v2-arns"></a>


| Resource | ARN | 
| --- | --- | 
|  AccessLogSettings  |  `arn:partition:apigateway:region::/apis/api-id/stages/stage-name/accesslogsettings`  | 
|  Api  |  `arn:partition:apigateway:region::/apis/api-id`  | 
|  Apis  |  `arn:partition:apigateway:region::/apis`  | 
|  ApiMapping  |  `arn:partition:apigateway:region::/domainnames/domain-name/apimappings/id`  | 
|  ApiMappings  |  `arn:partition:apigateway:region::/domainnames/domain-name/apimappings`  | 
|  Authorizer  |  `arn:partition:apigateway:region::/apis/api-id/authorizers/id`  | 
|  Authorizers  |  `arn:partition:apigateway:region::/apis/api-id/authorizers`  | 
|  Cors  |  `arn:partition:apigateway:region::/apis/api-id/cors`  | 
|  Deployment  |  `arn:partition:apigateway:region::/apis/api-id/deployments/id`  | 
|  Deployments  |  `arn:partition:apigateway:region::/apis/api-id/deployments`  | 
|  DomainName  |  `arn:partition:apigateway:region::/domainnames/domain-name`  | 
|  DomainNames  |  `arn:partition:apigateway:region::/domainnames`  | 
|  ExportedAPI  |  `arn:partition:apigateway:region::/apis/api-id/exports/specification`  | 
|  Integration  |  `arn:partition:apigateway:region::/apis/api-id/integrations/integration-id`  | 
|  Integrations  |  `arn:partition:apigateway:region::/apis/api-id/integrations`  | 
|  IntegrationResponse  |  `arn:partition:apigateway:region::/apis/api-id/integrationresponses/integration-response`  | 
|  IntegrationResponses  |  `arn:partition:apigateway:region::/apis/api-id/integrationresponses`  | 
|  Model  |  `arn:partition:apigateway:region::/apis/api-id/models/id`  | 
|  Models  |  `arn:partition:apigateway:region::/apis/api-id/models`  | 
|  ModelTemplate  |  `arn:partition:apigateway:region::/apis/api-id/models/id/template`  | 
|  Route  |  `arn:partition:apigateway:region::/apis/api-id/routes/id`  | 
|  Routes  |  `arn:partition:apigateway:region::/apis/api-id/routes`  | 
|  RouteRequestParameter  |  `arn:partition:apigateway:region::/apis/api-id/routes/id/requestparameters/key`  | 
|  RouteResponse  |  `arn:partition:apigateway:region::/apis/api-id/routes/id/routeresponses/id`  | 
|  RouteResponses  |  `arn:partition:apigateway:region::/apis/api-id/routes/id/routeresponses`  | 
|  RouteSettings  |  `arn:partition:apigateway:region::/apis/api-id/stages/stage-name/routesettings/route-key`  | 
|  Stage  |  `arn:partition:apigateway:region::/apis/api-id/stages/stage-name`  | 
|  Stages  |  `arn:partition:apigateway:region::/apis/api-id/stages`  | 
|  VpcLink  |  `arn:partition:apigateway:region::/vpclinks/vpclink-id`  | 
|  VpcLinks  |  `arn:partition:apigateway:region::/vpclinks`  | 

## REST API resources<a name="apigateway-v1-arns"></a>


| Resource | ARN | 
| --- | --- | 
|  ApiKey  |  `arn:partition:apigateway:region::/apikeys/id`  | 
|  ApiKeys  |  `arn:partition:apigateway:region::/apikeys`  | 
|  Authorizer  |  `arn:partition:apigateway:region::/restapis/api-id/authorizers/id`  | 
|  Authorizers  |  `arn:partition:apigateway:region::/restapis/api-id/authorizers`  | 
|  BasePathMapping  |  `arn:partition:apigateway:region::/domainnames/domain-name/basepathmappings/basepath`  | 
|  BasePathMappings  |  `arn:partition:apigateway:region::/domainnames/domain-name/basepathmappings`  | 
|  ClientCertificate  |  `arn:partition:apigateway:region::/clientcertificates/id`  | 
|  ClientCertificates  |  `arn:partition:apigateway:region::/clientcertificates`  | 
|  Deployment  |  `arn:partition:apigateway:region::/restapis/api-id/deployments/id`  | 
|  Deployments  |  `arn:partition:apigateway:region::/restapis/api-id/deployments`  | 
|  DocumentationPart  |  `arn:partition:apigateway:region::/restapis/api-id/documentation/parts/id`  | 
|  DocumentationParts  |  `arn:partition:apigateway:region::/restapis/api-id/documentation/parts`  | 
|  DocumentationVersion  |  `arn:partition:apigateway:region::/restapis/api-id/documentation/versions/version`  | 
|  DocumentationVersions  |  `arn:partition:apigateway:region::/restapis/api-id/documentation/versions`  | 
|  DomainName  |  `arn:partition:apigateway:region::/domainnames/domain-name`  | 
|  DomainNames  |  `arn:partition:apigateway:region::/domainnames`  | 
|  GatewayResponse  |  `arn:partition:apigateway:region::/restapis/api-id/gatewayresponses/response-type`  | 
|  GatewayResponses  |  `arn:partition:apigateway:region::/restapis/api-id/gatewayresponses`  | 
|  Integration  |  `arn:partition:apigateway:region::/restapis/api-id/resources/resource-id/methods/http-method/integration`  | 
|  IntegrationResponse  |  `arn:partition:apigateway:region::/restapis/api-id/resources/resource-id/methods/http-method/integration/responses/status-code`  | 
|  Method  |  `arn:partition:apigateway:region::/restapis/api-id/resources/resource-id/methods/http-method`  | 
|  MethodResponse  |  `arn:partition:apigateway:region::/restapis/api-id/resources/resource-id/methods/http-method/responses/status-code`  | 
|  Model  |  `arn:partition:apigateway:region::/restapis/api-id/models/model-name`  | 
|  Models  |  `arn:partition:apigateway:region::/restapis/api-id/models`  | 
|  RequestValidator  |  `arn:partition:apigateway:region::/restapis/api-id/requestvalidators/id`  | 
|  RequestValidators  |  `arn:partition:apigateway:region::/restapis/api-id/requestvalidators`  | 
|  Resource  |  `arn:partition:apigateway:region::/restapis/api-id/resources/id`  | 
|  Resources  |  `arn:partition:apigateway:region::/restapis/api-id/resources`  | 
|  RestApi  |  `arn:partition:apigateway:region::/restapis/api-id`  | 
|  RestApis  |  `arn:partition:apigateway:region::/restapis`  | 
|  Stage  |  `arn:partition:apigateway:region::/restapis/api-id/stages/stage-name`  | 
|  Stages  |  `arn:partition:apigateway:region::/restapis/api-id/stages`  | 
|  Template  |  `arn:partition:apigateway:region::/restapis/models/model-name/template`  | 
|  UsagePlan  |  `arn:partition:apigateway:region::/usageplans/usageplan-id`  | 
|  UsagePlans  |  `arn:partition:apigateway:region::/usageplans`  | 
|  UsagePlanKey  |  `arn:partition:apigateway:region::/usageplans/usageplan-id/keys/id`  | 
|  VpcLink  |  `arn:partition:apigateway:region::/vpclinks/vpclink-id`  | 
|  VpcLinks  |  `arn:partition:apigateway:region::/vpclinks`  | 

## `execute-api` \(HTTP API, WebSocket API, and REST API endpoints\)<a name="apigateway-execute-api-arns"></a>


| Resource | ARN | 
| --- | --- | 
|  WebSocket API endpoint  |  `arn:partition:execute-api:region:account-id:api-id/stage/route-key`  | 
|  HTTP API and REST API endpoint  |  `arn:partition:execute-api:region:account-id:api-id/stage/http-method/resource-path`  | 