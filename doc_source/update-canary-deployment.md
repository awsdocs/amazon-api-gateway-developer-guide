# Update a canary release<a name="update-canary-deployment"></a>

 After a canary release is deployed, you may want to adjust the percentage of the canary traffic or enable or disable the use of a stage cache to optimize the test performance\. You can also modify stage variables used in the canary release when the execution context is updated\. To make such updates, call the [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/) operation with new values on [canarySettings](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings)\. 

You can update a canary release using the API Gateway console, the AWS CLI [update\-stage](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-stage.html) command or an AWS SDK\.

**Topics**
+ [Update a canary release using the API Gateway console](#update-canary-deployment-using-console)
+ [Update a canary release using the AWS CLI](#update-canary-deployment-using-cli)

## Update a canary release using the API Gateway console<a name="update-canary-deployment-using-console"></a>

To use the API Gateway console to update existing canary settings on a stage, do the following:

1.  Sign in to the API Gateway console and choose an existing API in the primary navigation pane\.

1.  Choose **Stages** under the API and then choose an existing stage under the **Stages** list to open the **Stage Editor**\.

1.  Choose the **Canary** tab in the **Stage Editor**\.

1.  Update **Percentage of requests directed to Canary** by increasing or decreasing the percentage number between 0\.0 and 100\.0, inclusive\. 

1.  Update **Canary Stage Variables**, including adding, removing, or modifying a desired stage variable\.

1.  Update the **Enable use of stage cache** option by selecting or clearing the check box\. 

1.  Save the changes\.

## Update a canary release using the AWS CLI<a name="update-canary-deployment-using-cli"></a>

 To use the AWS CLI to update a canary, call the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-stage.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-stage.html) command\.

To enable or disable the use of a stage cache for the canary, call the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-stage.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-stage.html) command as follows: 

```
aws apigateway update-stage          \
    --rest-api-id {rest-api-id}      \
    --stage-name '{stage-name}'      \
    --patch-operations op=replace,path=/canarySettings/useStageCache,value=true
```

To adjust the canary traffic percentage, call `update-stage` to replace the `/canarySettings/percentTraffic` value on the [stage](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/)\.

```
aws apigateway update-stage          \
    --rest-api-id {rest-api-id}      \
    --stage-name '{stage-name}'      \
    --patch-operations op=replace,path=/canarySettings/percentTraffic,value=25.0
```

To update canary stage variables, including adding, replacing, or removing a canary stage variable:

```
aws apigateway update-stage                                     \
    --rest-api-id {rest-api-id}                                 \
    --stage-name '{stage-name}'                                 \
    --patch-operations '[{                                      \
        "op": "replace",                                        \
        "path": "/canarySettings/stageVariableOverides/newVar"  \
        "value": "newVal",                                      \
      }, {                                                      \
        "op": "replace",                                        \
        "path": "/canarySettings/stageVariableOverides/var2"    \
        "value": "val4",                                        \
      }, {                                                      \
        "op": "remove",                                         \
        "path": "/canarySettings/stageVariableOverides/var1"    \
      }]'
```

You can update all of the above by combining the operations into a single `patch-operations` value:

```
aws apigateway update-stage                                      \
    --rest-api-id {rest-api-id}                                  \
    --stage-name '{stage-name}'                                  \
    --patch-operations '[{                                       \
        "op": "replace",                                         \
        "path": "/canary/percentTraffic",                        \
        "value": "20.0"                                          \
    }, {                                                         \
        "op": "replace",                                         \
        "path": "/canary/useStageCache",                         \
        "value": "true"                                          \
    }, {                                                         \
        "op": "remove",                                          \
        "path": "/canarySettings/stageVariableOverrides/var1"    \
    }, {                                                         \
        "op": "replace",                                         \
        "path": "/canarySettings/stageVariableOverrides/newVar", \
        "value": "newVal"                                        \
    }, {                                                         \
        "op": "replace",                                         \
        "path": "/canarySettings/stageVariableOverrides/val2",   \
        "value": "val4"                                          \
      }]'
```