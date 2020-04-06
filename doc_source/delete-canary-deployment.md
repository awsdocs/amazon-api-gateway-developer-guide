# Disable a canary release<a name="delete-canary-deployment"></a>

To disable a canary release deployment is to set the [https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings) to null to remove it from the stage\. 

You can disable a canary release deployment using the API Gateway console, the AWS CLI, or an AWS SDK\.

**Topics**
+ [Disable a canary release using the API Gateway console](#delete-canary-release-console)
+ [Disable a canary release using the AWS CLI](#delete-canary-release-cli)

## Disable a canary release using the API Gateway console<a name="delete-canary-release-console"></a>

To use the API Gateway console to disable a canary release deployment, use the following steps:

1.  Sign in to the API Gateway console and choose an existing API in the primary navigation pane\.

1.  Choose **Stages** under the API and then choose an existing stage under the **Stages** list to open the **Stage Editor**\.

1.  Choose the **Canary** tab in the **Stage Editor**\.

1.  Choose **Delete Canary**\.

1.  Confirm you want to delete the canary by choosing **Delete**\.

As a result, the [https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings) property becomes `null` and is removed from the deployment [stage](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/)\. You can verify this using the AWS CLI\. For example, see [Disable a canary release using the AWS CLI](#delete-canary-release-cli)\.

## Disable a canary release using the AWS CLI<a name="delete-canary-release-cli"></a>

To use the AWS CLI to disable a canary release deployment, call the `update-stage` command as follows:

```
aws apigateway update-stage \
    --rest-api-id 4wk1k4onj3 \
    --stage-name canary \
    --patch-operations '[{"op":"remove", "path":"/canarySettings"}]'
```

A successful response returns a payload similar to the following:

```
{
    "stageName": "prod", 
    "accessLogSettings": {
        ...
    }, 
    "cacheClusterEnabled": false, 
    "cacheClusterStatus": "NOT_AVAILABLE", 
    "deploymentId": "nfcn0x", 
    "lastUpdatedDate": 1511309280, 
    "createdDate": 1511152939, 
    "methodSettings": {
        ...
    }
}
```

 As shown in the output, the [https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings) property is no longer present in the [stage](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/) of a canary\-disabled deployment\.