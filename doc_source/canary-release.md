# Set up an API Gateway canary release deployment<a name="canary-release"></a>

[Canary release](https://martinfowler.com/bliki/CanaryRelease.html) is a software development strategy in which a new version of an API \(as well as other software\) is deployed for testing purposes, and the base version remains deployed as a production release for normal operations on the same stage\. For purposes of discussion, we refer to the base version as a production release in this documentation\. Although this is reasonable, you are free to apply canary release on any non\-production version for testing\.

In a canary release deployment, total API traffic is separated at random into a production release and a canary release with a pre\-configured ratio\. Typically, the canary release receives a small percentage of API traffic and the production release takes up the rest\. The updated API features are only visible to API traffic through the canary\. You can adjust the canary traffic percentage to optimize test coverage or performance\. 

By keeping canary traffic small and the selection random, most users are not adversely affected at any time by potential bugs in the new version, and no single user is adversely affected all the time\.

After the test metrics pass your requirements, you can promote the canary release to the production release and disable the canary from the deployment\. This makes the new features available in the production stage\. 

**Topics**
+ [Canary release deployment in API Gateway](#api-gateway-canary-release-deployment-overview)
+ [Create a canary release deployment](create-canary-deployment.md)
+ [Update a canary release](update-canary-deployment.md)
+ [Promote a canary release](promote-canary-deployment.md)
+ [Disable a canary release](delete-canary-deployment.md)

## Canary release deployment in API Gateway<a name="api-gateway-canary-release-deployment-overview"></a>

 In API Gateway, a canary release deployment uses the deployment stage for the production release of the base version of an API, and attaches to the stage a canary release for the new versions, relative to the base version, of the API\. The stage is associated with the initial deployment and the canary with subsequent deployments\. At the beginning, both the stage and the canary point to the same API version\. We use stage and production release interchangeably and use canary and canary release interchangeably throughout this section\.

To deploy an API with a canary release, you create a canary release deployment by adding [canary settings](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings) to the [stage](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/) of a regular [deployment](https://docs.aws.amazon.com/apigateway/api-reference/resource/deployment/)\. The canary settings describe the underlying canary release and the stage represents the production release of the API within this deployment\. To add canary settings, set `canarySettings` on the deployment stage and specify the following: 
+  A deployment ID, initially identical to the ID of the base version deployment set on the stage\. 
+  A [percentage of API traffic](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#percentTraffic), between 0\.0 and 100\.0 inclusive, for the canary release\. 
+  [Stage variables for the canary release](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#stageVariableOverrides) that can override production release stage variables\. 
+  The [use of the stage cache](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#useStageCache) for canary requests, if the [useStageCache](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#useStageCache) is set and API caching is enabled on the stage\.

 After a canary release is enabled, the deployment stage cannot be associated with another non\-canary release deployment until the canary release is disabled and the canary settings removed from the stage\. 

When you enable API execution logging, the canary release has its own logs and metrics generated for all canary requests\. They are reported to a production stage CloudWatch Logs log group as well as a canary\-specific CloudWatch Logs log group\. The same applies to access logging\. The separate canary\-specific logs are helpful to validate new API changes and decide whether to accept the changes and promote the canary release to the production stage, or to discard the changes and revert the canary release from the production stage\.

The production stage execution log group is named `API-Gateway-Execution-Logs/{rest-api-id}/{stage-name}` and the canary release execution log group is named `API-Gateway-Execution-Logs/{rest-api-id}/{stage-name}/Canary`\. For access logging, you must create a new log group or choose an existing one\. The canary release access log group name has the `/Canary` suffix appended to the selected log group name\. 

A canary release can use the stage cache, if enabled, to store responses and use cached entries to return results to the next canary requests, within a pre\-configured time\-to\-live \(TTL\) period\. 

In a canary release deployment, the production release and canary release of the API can be associated with the same version or with different versions\. When they are associated with different versions, responses for production and canary requests are cached separately and the stage cache returns corresponding results for production and canary requests\. When the production release and canary release are associated with the same deployment, the stage cache uses a single cache key for both types of requests and returns the same response for the same requests from the production release and canary release\. 