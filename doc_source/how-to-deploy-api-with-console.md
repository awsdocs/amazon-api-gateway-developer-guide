# Deploy a REST API from the API Gateway Console<a name="how-to-deploy-api-with-console"></a>

 You must have created a REST API before deploying it for the first time\. For more information see [Creating a REST API in Amazon API Gateway](how-to-create-api.md)\. 

**Topics**
+ [Deploy a REST API to a Stage](#how-to-deploy-api-console)
+ [Redeploy a REST API to a Stage](#apigateway-how-to-redeploy-api-console)
+ [Update the Stage Configuration of a REST API Deployment](#how-to-deploy-api-update-stage-settings)
+ [Set Stage Variables for a REST API Deployment](#how-to-deploy-api-set-stage-variables)
+ [Associate a Stage with a Different REST API Deployment](#how-to-deploy-change-deployment-console)

## Deploy a REST API to a Stage<a name="how-to-deploy-api-console"></a>

 The API Gateway console lets you deploy an API by creating a deployment and associating it with a new or existing stage\. 

**Note**  
To associate a stage in API Gateway with a different deployment, see [Associate a Stage with a Different REST API Deployment](#how-to-deploy-change-deployment-console) instead\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1.  In the **APIs** navigation pane, choose the API you want to deploy\. 

1. In the **Resources** navigation pane, choose **Actions**\.

1. From the **Actions** drop\-down menu, choose **Deploy API**\.

1. In the **Deploy API** dialog, choose an entry from the **Deployment stage** dropdown list\. 

1. If you choose **\[New Stage\]**, type a name in **Stage name** and optionally provide a description for the stage and deployment in **Stage description** and **Deployment description**\. If you choose an existing stage, you may want to provide a description of the new deployment in **Deployment description**\.

1. Choose **Deploy** to deploy the API to the specified stage with default stage settings\.

## Redeploy a REST API to a Stage<a name="apigateway-how-to-redeploy-api-console"></a>

To redeploy an API, perform the same steps as in [Deploy a REST API to a Stage](#how-to-deploy-api-console)\. You can reuse the same stage as many times as desired\.

## Update the Stage Configuration of a REST API Deployment<a name="how-to-deploy-api-update-stage-settings"></a>

 After an API is deployed, you can modify the stage settings to enable or disable API cache, logging, or request throttling\. You can also choose a client certificate for the backend to authenticate API Gateway and set stage variables to pass deployment context to the API integration at run time\. For more information, see [Update Stage Settings](stages.md#how-to-stage-settings)\. 

**Important**  
After modifying stage settings, you must redeploy the API for the changes to take effect\.

**Note**  
 If the updated settings, such as enabling logging, requires a new IAM role, you can add the required IAM role without redeploying the API\. However, it can take a few minutes before the new IAM role takes effect\. Before that happens, traces of your API calls will not be logged even if you have enabled the logging option\. 

## Set Stage Variables for a REST API Deployment<a name="how-to-deploy-api-set-stage-variables"></a>

 For a deployment, you can set or modify stage variables to pass deployment\-specific data to the API integration at run time\. You can do this on the **Stage Variables** tab in the **Stage Editor**\. For more information, see instructions in [Set up Stage Variables for a REST API Deployment](stage-variables.md)\. 

## Associate a Stage with a Different REST API Deployment<a name="how-to-deploy-change-deployment-console"></a>

 Because a deployment represents an API snapshot and a stage defines a path into a snapshot, you can choose different deployment\-stage combinations to control how users call into different versions of the API\. This is useful, for example, when you want to roll back API state to a previous deployment or to merge a 'private branch' of the API into the public one\. 

 The following procedure shows how to do this using the **Stage Editor** in the API Gateway console\. It is assumed that you must have deployed an API more than once\. 

1. If not already in **Stage Editor**, choose the stage you want to update the deployment from an API's **Stages** option in the **APIs** main navigation pane\.

1. On the **Deployment History** tab, choose the option button next to the deployment you want the stage to use\.

1. Choose **Change Deployment**\.