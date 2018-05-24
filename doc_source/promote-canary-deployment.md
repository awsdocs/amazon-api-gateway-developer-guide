# Promote a Canary Release<a name="promote-canary-deployment"></a>

To promote a canary release makes it available in the production stage the API version under testing\. The operation involves the following tasks:
+  Reset the [deployment ID](http://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#deploymentId) of the stage with the [deployment ID](http://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings) settings of the canary\. This updates the API snapshot of the stage with the snapshot of the canary, making the test version the production release as well\.
+  Update stage variables with canary stage variables, if any\. This updates the API execution context of the stage with that of the canary\. Without this update, the new API version may produce unexpected results if the test version uses different stage variables or different values of existing stage variables\.
+  Set the percentage of canary traffic to 0\.0%\.

Promoting a canary release does not disable the canary on the stage\. To disable a canary, you must remove the canary settings on the stage\.

**Topics**
+ [Promote a Canary Release Using the API Gateway Console](#promote-canary-release-deployment-console)
+ [Promote a Canary Release Using the AWS CLI](#promote-canary-release-cli)
+ [Promote a Canary Release Using the API Gateway REST API](#promote-canary-release-api)

## Promote a Canary Release Using the API Gateway Console<a name="promote-canary-release-deployment-console"></a>

To use the API Gateway console to promote a canary release deployment, do the following:

1.  Sign in to the API Gateway console and choose an existing API in the primary navigation pane\.

1.  Choose **Stages** under the API and then choose an existing stage under the **Stages** list to open the **Stage Editor**\.

1.  Choose the **Canary** tab in the **Stage Editor**\.

1.  Choose **Promote Canary**\.

1.  Confirm changes to be made and choose **Update**\.

After the promotion, the production release references the same API version \(**deploymentId**\) as the canary release\. You can verify this using the AWS CLI or API Gateway REST API\. For example, see [Promote a Canary Release Using the AWS CLI](#promote-canary-release-cli) or [Promote a Canary Release Using the API Gateway REST API](#promote-canary-release-api)\. 

## Promote a Canary Release Using the AWS CLI<a name="promote-canary-release-cli"></a>

To promote a canary release to the production release using the AWS CLI commands, call the `update-stage` command to copy the canary\-associated `deploymentId` to the stage\-associated `deploymentId`, to reset the canary traffic percentage to zero \(`0.0`\), and, to copy any canary\-bound stage variables to the corresponding stage\-bound ones\. 

Suppose we have a canary release deployment, described by a stage similar to the following: 

```
{
    "_links": {
        ...
    },
    "accessLogSettings": {
        ...
    },
    "cacheClusterEnabled": false,
    "cacheClusterStatus": "NOT_AVAILABLE",
    "canarySettings": {
        "deploymentId": "eh1sby",
        "useStageCache": false,
        "stageVariableOverrides": {
            "sv2": "val3",
            "sv1": "val2"
        },
        "percentTraffic": 10.5
    },
    "createdDate": "2017-11-20T04:42:19Z",
    "deploymentId": "nfcn0x",
    "lastUpdatedDate": "2017-11-22T00:54:28Z",
    "methodSettings": {
        ...
    },
    "stageName": "prod",
    "variables": {
        "sv1": "val1"
    }
}
```

We call the following `update-stage` request to promote it:

```
aws apigateway update-stage                         \
    --rest-api-id {rest-api-id}                     \
    --stage-name '{stage-name}'                     \
    --patch-operations '[{                          \
        "op": "replace",                            \
        "value": "0.0"                              \
        "path": "/canarySettings/percentTraffic",   \
      }, {                                          \
        "op": "copy",                               \
        "from": "/canary/overriddenStageVariables"  \
        "path": "/variables",                       \
      }, {                                          \
        "op": "copy",                               \
        "from": "/canary/deploymentId",             \
        "path": "/deploymentId"                     \
      }]'
```

After the promotion, the stage now looks like this:

```
{
    "_links": {
        ...
    },
    "accessLogSettings": {
        ...
    },
    "cacheClusterEnabled": false,
    "cacheClusterStatus": "NOT_AVAILABLE",
    "canarySettings": {
        "deploymentId": "eh1sby",
        "useStageCache": false,
        "stageVariableOverrides": {
            "sv2": "val3",
            "sv1": "val2"
        },
        "percentTraffic": 0
    },
    "createdDate": "2017-11-20T04:42:19Z",
    "deploymentId": "eh1sby",
    "lastUpdatedDate": "2017-11-22T05:29:47Z",
    "methodSettings": {
        ...
    },
    "stageName": "prod",
    "variables": {
        "sv2": "val3",
        "sv1": "val2"
    }
}
```

As you can see, promoting a canary release to the stage does not disable the canary and the deployment remains to be a canary release deployment\. To make it a regular production release deployment, you must disable the canary settings\. For more information about how to disable a canary release deployment, see [Disable a Canary Release](delete-canary-deployment.md)\.

## Promote a Canary Release Using the API Gateway REST API<a name="promote-canary-release-api"></a>

To promote a canary release to the production release using the API Gateway REST API, call the `stage:update` request to copy the canary\-associated `deploymentId` to the stage\-associated `deploymentId`, to reset the canary traffic percentage to zero \(`0.0`\), and, to copy any canary\-bound stage variables to the corresponding stage\-bound ones\. 

Suppose we have a canary release deployment, described by a stage similar to the following: 

```
{
    "_links": {
        ...
    },
    "accessLogSettings": {
        ...
    },
    "cacheClusterEnabled": false,
    "cacheClusterStatus": "NOT_AVAILABLE",
    "canarySettings": {
        "deploymentId": "eh1sby",
        "useStageCache": false,
        "stageVariableOverrides": {
            "sv2": "val3",
            "sv1": "val2"
        },
        "percentTraffic": 10.5
    },
    "createdDate": "2017-11-20T04:42:19Z",
    "deploymentId": "nfcn0x",
    "lastUpdatedDate": "2017-11-22T00:54:28Z",
    "methodSettings": {
        ...
    },
    "stageName": "prod",
    "variables": {
        "sv1": "val1"
    }
}
```

We call the following `stage:update` request to promote it:

```
PATCH /restapis/4wk1k4onj3/stages/prod HTTP/1.1
Host: apigateway.us-east-1.amazonaws.com
Content-Type: application/json
X-Amz-Date: 20171121T232431Z
Authorization: AWS4-HMAC-SHA256 Credential={SECRET_ACCESS_KEY}/20171121/us-east-1/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature={SIGV4_SIGNATURE}

{
	"patchOperations": [
		{
			"op": "copy",
			"path": "/deploymentId",
			"from": "/canarySettings/deploymentId"
		},
		{
			"op": "replace",
			"path": "/canarySettings/percentTraffic",
			"value": "0.0"
		},
		{
			"op": "copy",
			"path": "/variables",
			"from": "/canarySettings/stageVariableOverrides"
		}
	]
}
```

After the promotion, the stage now looks like this:

```
{
    "_links": {
        ...
    },
    "accessLogSettings": {
        ...
    },
    "cacheClusterEnabled": false,
    "cacheClusterStatus": "NOT_AVAILABLE",
    "canarySettings": {
        "deploymentId": "eh1sby",
        "useStageCache": false,
        "stageVariableOverrides": {
            "sv2": "val3",
            "sv1": "val2"
        },
        "percentTraffic": 0
    },
    "createdDate": "2017-11-20T04:42:19Z",
    "deploymentId": "eh1sby",
    "lastUpdatedDate": "2017-11-22T05:29:47Z",
    "methodSettings": {
        ...
    },
    "stageName": "prod",
    "variables": {
        "sv2": "val3",
        "sv1": "val2"
    }
}
```

As you can see, promoting a canary release to the stage does not disable the canary and the deployment remains to be a canary release deployment\. To make it a regular production release deployment, you must disable the canary settings\. For more information about how to disable a canary release deployment, see [Disable a Canary Release](delete-canary-deployment.md)\.