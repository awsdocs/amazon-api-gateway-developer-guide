# Deploy a WebSocket API in API Gateway<a name="apigateway-set-up-websocket-deployment"></a>

 After creating your WebSocket API, you must deploy it to make it available for your users to invoke\. 

To deploy an API, you create an [API deployment](api-gateway-basic-concept.md#apigateway-definition-api-deployment) and associate it with a [stage](api-gateway-basic-concept.md#apigateway-definition-api-stage)\. Each stage is a snapshot of the API and is made available for client apps to call\. 

**Important**  
Every time you update an API, which includes modification of routes, methods, integrations, authorizers, and anything else other than stage settings, you must redeploy the API to an existing stage or to a new stage\.   
By default you are limited to 10 stages per API, so it is good practice to reuse them\.

To call a deployed WebSocket API, the client sends a message to the API's URL\. The URL is determined by the API's hostname and stage name\.

**Note**  
API Gateway will support payloads up to 128 KB with a maximum frame size of 32 KB\. If a message exceeds 32 KB, it must be split into multiple frames, each 32 KB or smaller\.

Using the API's default domain name, the URL of \(for example\) a WebSocket API in a given stage \(`{stageName}`\) is in the following format:

```
wss://{api-id}.execute-api.{region}.amazonaws.com/{stageName}
```

To make the WebSocket API's URL more user\-friendly, you can create a custom domain name \(e\.g\., `api.example.com`\) to replace the default host name of the API\. The configuration process is the same as for REST APIs\. For more information, see [Setting up custom domain names for REST APIs](how-to-custom-domains.md)\.

Stages enable robust version control of your API\. For example, you can deploy an API to a `test` stage and a `prod` stage, and use the `test` stage as a test build and use the `prod` stage as a stable build\. After the updates pass the test, you can promote the `test` stage to the `prod` stage\. The promotion can be done by redeploying the API to the `prod` stage\. For more details about stages, see [Setting up a stage for a REST API](set-up-stages.md)\.

**Topics**
+ [Create a WebSocket API deployment using the AWS CLI](#apigateway-create-websocket-deployment-using-awscli)
+ [Create a WebSocket API deployment using the API Gateway console](#apigateway-create-websocket-deployment-using-console)

## Create a WebSocket API deployment using the AWS CLI<a name="apigateway-create-websocket-deployment-using-awscli"></a>

To use AWS CLI to create a deployment, use the [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateDeployment](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateDeployment) command as shown in the following example:

```
aws apigatewayv2 --region us-east-1 create-deployment --api-id aabbccddee
```

Example output:

```
{
    "DeploymentId": "fedcba",
    "DeploymentStatus": "DEPLOYED",
    "CreatedDate": "2018-11-15T06:49:09Z"
}
```

The deployed API is not callable until you associate the deployment with a stage\. You can create a new stage or reuse a stage that you have previously created\.

To create a new stage and associate it with the deployment, use the [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateStage](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateStage) command as shown in the following example:

```
aws apigatewayv2 --region us-east-1 create-stage --api-id aabbccddee --deployment-id fedcba --stage-name test
```

Example output:

```
{
    "StageName": "test",
    "CreatedDate": "2018-11-15T06:50:28Z",
    "DeploymentId": "fedcba",
    "DefaultRouteSettings": {
        "MetricsEnabled": false,
        "ThrottlingBurstLimit": 5000,
        "DataTraceEnabled": false,
        "ThrottlingRateLimit": 10000.0
    },
    "LastUpdatedDate": "2018-11-15T06:50:28Z",
    "StageVariables": {},
    "RouteSettings": {}
}
```

To reuse an existing stage, update the stage's `deploymentId` property with the newly created deployment ID \(`{deployment-id}`\) by using the [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/UpdateStage](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/UpdateStage) command\.

```
 aws apigatewayv2 update-stage --region {region} \
    --api-id {api-id} \ 
    --stage-name {stage-name} \ 
    --deployment-id {deployment-id}
```

## Create a WebSocket API deployment using the API Gateway console<a name="apigateway-create-websocket-deployment-using-console"></a>

To use the API Gateway console to create a deployment for a WebSocket API:

1. Sign in to the API Gateway console and choose the API\.

1. From the **Actions** dropdown menu, choose **Deploy API**\.

1. Choose the desired stage from the dropdown list or enter the name of a new stage\.