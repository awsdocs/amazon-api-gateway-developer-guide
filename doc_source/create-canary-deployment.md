# Create a Canary Release Deployment<a name="create-canary-deployment"></a>

You create a canary release deployment when deploying the API with [canary settings](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/#canarySettings) as an additional input to the [deployment creation](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/) operation\. 

You can also create a canary release deployment from an existing non\-canary deployment by making a [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/) request to add the canary settings on the stage\.

When creating a non\-canary release deployment, you can specify a non\-existing stage name\. API Gateway creates one if the specified stage does not exist\. However, you cannot specify any non\-existing stage name when creating a canary release deployment\. You will get an error and API Gateway will not create any canary release deployment\. 

 You can create a canary release deployment in API Gateway using the API Gateway console, AWS CLI, an AWS SDK, and the API Gateway REST API\.

**Topics**
+ [Create a Canary Deployment Using the API Gateway Console](#create-canary-deployment-using-console)
+ [Create a Canary Deployment Using the AWS CLI](#create-canary-deployment-using-cli)
+ [Create a Canary Deployment Using the API Gateway API](#create-canary-deployment-using-api)

## Create a Canary Deployment Using the API Gateway Console<a name="create-canary-deployment-using-console"></a>

To use the API Gateway console to create a canary release deployment, follow the instructions below:<a name="to-create-canary-release-on-new-deployment"></a>

**To create the initial canary release deployment**

1.  Sign in to the API Gateway console\.

1.  Choose an existing API or create a new API\.

1.  Change the API, if necessary, or set up desired API methods and integrations\.

1.  Choose **Deploy API** from the **Actions** drop\-down menu\. Follow the on\-screen instructions in **Deploy API** to deploy the API to a new stage\. 

   So far, you have deployed the API to a production release stage\. Next, you configure canary settings on the stage and, if needed, also enable caching, set stage variables, or configure API execution or access logs\.

1.  To enable API caching, choose the **Settings** tab in **Stage Editor** and follow the on\-screen instructions\. For more information, see [Enable API Caching to Enhance Responsiveness](api-gateway-caching.md)\.

1. To set stage variables, choose the **Stage Variables** tab in **Stage Editor** and follow the on\-screen instructions to add or modify stage variables\. For more information, see [Set up Stage Variables for a REST API Deployment](stage-variables.md)\.

1.  To configure execution or access logging, choose the **Logs** tab in **Stage Editor** and follow the on\-screen instructions\. For more information, see [Set Up CloudWatch API Logging in API Gateway](set-up-logging.md)\.

1.  In **Stage Editor**, choose the **Canary** tab and then choose **Create Canary**\.

1.  Under the **Stage's Request Distribution** section, choose the pencil icon next to **Percentage of requests to Canary** and type a number \(for example, `5.0`\) in the input text field\. Choose the check mark icon to save the setting\.

1. To associate an AWS WAF web ACL with the stage, choose a web ACL from the **Web ACL** dropdown list\.
**Note**  
If needed, choose **Create Web ACL** to open the AWS WAF console in a new browser tab, create the web ACL, and return to the API Gateway console to associate the web ACL with the stage\.

1. If desired, choose **Block API Request if WebACL cannot be evaluated \(Fail\- Close\)**\.

1. If needed, choose **Add Stage Variables** to add them under the **Canary Stage Variables** section to override existing stage variables or add new stage variables for the canary release\. 

1. If desired, choose **Enable use of stage cache** to enable caching for the canary release and save your choice\. The cache is not available for the canary release until API caching is enabled\.

After the canary release is initialized on the deployment stage, you change the API and want to test the changes\. You can redeploy the API to the same stage so that both the updated version and the base version are accessible through the same stage\. The following steps describe how to do that\. <a name="to-deploy-latest-api-to-canary-release"></a>

**To deploy the latest API version to a canary**

1.  With each update of the API, choose **Deploy API** from the **Actions** drop\-down menu next to the **Resources** list\.

1.  In **Deploy API**, choose the now canary\-enabled stage from the **Deployment stage** drop\-down list\. 

1.  Optionally, type a description in ** Deployment description**\. 

1.  Choose **Deploy** to push the latest API version to the canary release\. 

1.  If desired, reconfigure the stage settings, logs, or canary settings, as describe in [To create the initial canary release deployment](#to-create-canary-release-on-new-deployment)\.

 As a result, the canary release points to the latest version while the production release still points to the initial version of the API\. The [https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#canarySettings) now has a new **deploymentId** value, whereas the stage still has the initial [https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#deploymentId](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#deploymentId) value\. Behind the scenes, the console calls [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/)\.

## Create a Canary Deployment Using the AWS CLI<a name="create-canary-deployment-using-cli"></a>

First create a baseline deployment with two stage variables, but without any canary:

```
aws apigateway create-deployment 
    --variables sv0=val0,sv1=val1
    --rest-api-id 4wk1k4onj3 
    --stage-name prod
```

The command returns a representation of the resulting [https://docs.aws.amazon.com/apigateway/api-reference/resource/deployment/](https://docs.aws.amazon.com/apigateway/api-reference/resource/deployment/), similar to the following:

```
{
    "id": "du4ot1", 
    "createdDate": 1511379050
}
```

The resulting deployment `id` identifies a snapshot \(or version\) of the API\. 

Now create a canary deployment on the `prod` stage: 

```
aws apigateway create-deployment  
    --canary-settings '{ \
        "percentTraffic":10.5, \
        "useStageCache":false, \
        "stageVariableOverrides":{ \
            "sv1":"val2",  \
            "sv2":"val3" \
        } \ 
    }' \
    --rest-api-id 4wk1k4onj3 \
    --stage-name prod
```

If the specified stage \(`prod`\) does not exist, the preceding command returns an error\. Otherwise, it returns the newly created [deployment](https://docs.aws.amazon.com/apigateway/api-reference/resource/deployment/) resource representation similar to the following:

```
{
    "id": "a6rox0", 
    "createdDate": 1511379433
}
```

The resulting deployment `id` identifies the test version of the API for the canary release\. As a result, the associated stage is canary\-enabled\. You can view this stage representation by calling the `get-stage` command, similar to the following:

```
aws apigateway get-stage --rest-api-id 4wk1k4onj3 --stage-name prod
```

The following shows a representation of the `Stage` as the output of the command:

```
{
    "stageName": "prod", 
    "variables": {
        "sv0": "val0", 
        "sv1": "val1"
    }, 
    "cacheClusterEnabled": false, 
    "cacheClusterStatus": "NOT_AVAILABLE", 
    "deploymentId": "du4ot1", 
    "lastUpdatedDate": 1511379433, 
    "createdDate": 1511379050, 
    "canarySettings": {
        "percentTraffic": 10.5, 
        "deploymentId": "a6rox0", 
        "useStageCache": false, 
        "stageVariableOverrides": {
            "sv2": "val3", 
            "sv1": "val2"
        }
    }, 
    "methodSettings": {}
}
```

In this example, the base version of the API will use the stage variables of `{"sv0":val0", "sv1":val1"}`, while the test version uses the stage variables of `{"sv1":val2", "sv2":val3"}`\. Both the production release and canary release use the same stage variable of `sv1`, but with different values, `val1` and `val2`, respectively\. The stage variable of `sv0` is used solely in the production release and the stage variable of `sv2` is used in the canary release only\. 

You can create a canary release deployment from an existing regular deployment by updating the stage to enable a canary\. To demonstrate this, create a regular deployment first:

```
aws apigateway create-deployment \
    --variables sv0=val0,sv1=val1 \  
    --rest-api-id 4wk1k4onj3 \
    --stage-name beta
```

The command returns a representation of the base version deployment:

```
{
    "id": "cifeiw", 
    "createdDate": 1511380879
}
```

The associated beta stage does not have any canary settings:

```
{
    "stageName": "beta", 
    "variables": {
        "sv0": "val0", 
        "sv1": "val1"
    }, 
    "cacheClusterEnabled": false, 
    "cacheClusterStatus": "NOT_AVAILABLE", 
    "deploymentId": "cifeiw", 
    "lastUpdatedDate": 1511380879, 
    "createdDate": 1511380879, 
    "methodSettings": {}
}
```

Now, create a new canary release deployment by attaching a canary on the stage:

```
aws apigateway update-stage  \
    --patch-operations '[{ \
        "op":"replace", \
        "path":"/canarySettings/percentTraffic", \
        "value":"10.5" \
    },{ \
        "op":"replace", \
        "path":"/canarySettings/useStageCache", \
        "value":"false" \
    },{ \
        "op":"replace", \
        "path":"/canarySettings/stageVariableOverrides/sv1", \
        "value":"val2" \
    },{ \
        "op":"replace", \
        "path":"/canarySettings/stageVariableOverrides/sv2", \
        "value":"val3" \
    }]' \
    --rest-api-id 4wk1k4onj3  \
    --stage-name beta
```

A representation of the updated stage looks like this:

```
{
    "stageName": "beta", 
    "variables": {
        "sv0": "val0", 
        "sv1": "val1"
    }, 
    "cacheClusterEnabled": false, 
    "cacheClusterStatus": "NOT_AVAILABLE", 
    "deploymentId": "cifeiw", 
    "lastUpdatedDate": 1511381930, 
    "createdDate": 1511380879, 
    "canarySettings": {
        "percentTraffic": 10.5, 
        "deploymentId": "cifeiw", 
        "useStageCache": false, 
        "stageVariableOverrides": {
            "sv2": "val3", 
            "sv1": "val2"
        }
    }, 
    "methodSettings": {}
}
```

Because we just enabled a canary on an existing version of the API, both the production release \(`Stage`\) and canary release \(`canarySettings`\) point to the same deployment, i\.e\., the same version \(`deploymentId`\) of the API\. After you change the API and deploy it to this stage again, the new version will be in the canary release, while the base version remains in the production release\. This is manifested in the stage evolution when the `deploymentId` in the canary release is updated to the new deployment `id` and the `deploymentId` in the production release remains unchanged\.

## Create a Canary Deployment Using the API Gateway API<a name="create-canary-deployment-using-api"></a>

To use the API Gateway REST API to deploy your API as a canary release, call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/) as follows:

```
POST /restapis/fugvjdxtri/deployments HTTP/1.1
Content-Type: application/json
Host: apigateway.us-east-1.amazonaws.com
X-Amz-Date: 20171103T175605Z
Authorization: AWS4-HMAC-SHA256 Credential={SECRET_ACCESS_KEY}/20160603/us-east-1/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature={SIGV4_SIGNATURE}

{
  "stageName" : "prod",
  "stageDescription" : "Production stage",
  "description" : "Production deployment with canary",
  "variables" : {
    "sv1" : "val1"
  },
  "canarySettings": {
    "percentTraffic": 10.5,
    "useStageCache": false,
    "stageVariableOverrides": {
        "sv1": "val2",
        "sv2": "val3"
    }
  }
}
```

If successful, and if this is the first time to deploy the API to the stage, you get a brand new canary release deployment \(`nfcn0x`\): 

```
{
    "_links": {
        ...
    },
    "createdDate": "2017-11-22T00:54:28Z",
    "description": "Production deployment with canary",
    "id": "nfcn0x"
}
```

In this deployment, both the stage and the canary have the same `deploymentId`\. That is, they both reference the same API version\. 

In any subsequent API deployments to the same stage, you must always specify `canarySettings` as an input, until the canary is disabled on that stage\. For example, when you call the previous `deployment:create` request the second time, you get a new deployment \(`eh1sby`\) as the result:

```
{
    "_links": {
        ...
    },
    "createdDate": "2017-11-22T01:24:23Z",
    "description": "Production deployment with canary",
    "id": "eh1sby"
}
```

The newer `deploymentId` value is set on the `canarySettings` and the canary represents the new API version, while the initial `deploymentId` remains associated with the stage that represents the initial API version\. You can verify this by calling the [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-by-name/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-by-name/) request and examining the successful response payload:

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
        "*/*": {
            "dataTraceEnabled": true,
            "throttlingRateLimit": 10000,
            "cacheTtlInSeconds": 300,
            "cachingEnabled": false,
            "requireAuthorizationForCacheControl": true,
            "metricsEnabled": true,
            "loggingLevel": "INFO",
            "unauthorizedCacheControlHeaderStrategy": "SUCCEED_WITH_RESPONSE_HEADER",
            "throttlingBurstLimit": 5000,
            "cacheDataEncrypted": false
        }
    },
    "stageName": "canary",
    "variables": {
        "sv1": "val1"
    }
}
```

For a regular production deployment without a canary enabled on the associated stage, you can turn the deployment into a canary release deployment by enabling the canary on the stage\. To do this, call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-update/), as shown in the following, assuming the original deployment ID is `ghdx4w`:

```
PATCH /restapis/4wk1k4onj3/stages/prod HTTP/1.1
Host: apigateway.us-east-1.amazonaws.com
Content-Type: application/json
X-Amz-Date: 20171121T232431Z
Authorization: AWS4-HMAC-SHA256 Credential={SECRET_ACCESS_KEY}/20171121/us-east-1/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature={SIGV4_SIGNATURE}

{
	"patchOperations": [
		{
			"op": "replace",
			"path": "/canarySettings/deploymentId",
			"value": "ghdx4w"
		},
		{
			"op": "replace",
			"path": "/canarySettings/percentTraffic",
			"value": "15.0"
		}
	]
}
```

Because the original deployment is without a canary release, we set the `/canarysettings/deploymentId` value to the `deploymentId` \(`"ghdx4w"`\) associated with the deployment stage\. 

A successful response returns a payload similar to the following:

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
        "deploymentId": "ghdx4w",
        "useStageCache": false,
        "stageVariableOverrides": null,
        "percentTraffic": 15
    },
    "createdDate": "2017-11-20T04:42:19Z",
    "deploymentId": "ghdx4w",
    "lastUpdatedDate": "2017-11-21T23:24:31Z",
    "methodSettings": {
        "*/*": {
            "dataTraceEnabled": true,
            "throttlingRateLimit": 10000,
            "cacheTtlInSeconds": 300,
            "cachingEnabled": false,
            "requireAuthorizationForCacheControl": true,
            "metricsEnabled": true,
            "loggingLevel": "INFO",
            "unauthorizedCacheControlHeaderStrategy": "SUCCEED_WITH_RESPONSE_HEADER",
            "throttlingBurstLimit": 5000,
            "cacheDataEncrypted": false
        }
    },
    "stageName": "prod"
}
```

With a canary enabled on the stage, the deployment becomes a canary release deployment\. The stage cannot be associated with any non\-canary deployment until the canary settings are removed from the stage\.