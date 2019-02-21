# Deploy a REST API in in API Gateway<a name="set-up-deployments"></a>

 In API Gateway, a REST API deployment is represented by a [Deployment](https://docs.aws.amazon.com/apigateway/api-reference/resource/deployment/) resource\. It is like an executable of an API represented by a [RestApi](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/) resource\. For the client to call your API, you must create a deployment and associate a stage with it\. A stage is represented by a [Stage](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/) resource and represents a snapshot of the API, including methods, integrations, models, mapping templates, Lambda authorizers \(formerly known as custom authorizers\), etc\. When you update the API, you can redeploy the API by associating a new stage with the existing deployment\. We discuss creating a stage in [Set up a Stage in API Gateway](set-up-stages.md)\.

**Topics**
+ [Create a Deployment Using AWS CLI](#create-deployment-using-cli)
+ [Deploy a REST API from the API Gateway Console](how-to-deploy-api-with-console.md)

## Create a Deployment Using AWS CLI<a name="create-deployment-using-cli"></a>

 Creating a deployment amounts to instantiating the [Deployment](https://docs.aws.amazon.com/apigateway/api-reference/resource/deployment/) resource\. You can use the API Gateway console, AWS CLI, an AWS SDK or the API Gateway REST API to create an deployment\. 

To use CLI to create a deployment, use the create\-deployment command:

```
 aws apigateway create-deployment --rest-api-id <rest-api-id> --region <region>
```

 The API is not callable until you associate this deployment with a stage\. With an existing stage, you can do so by updating the stage's [https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#deploymentId](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/#deploymentId) property with the newly created deployment ID \(`<deployment-id>`\)\.

```
 aws apigateway update-stage --region <region> \
    --rest-api-id <rest-api-id> \ 
    --stage-name <stage-name> \ 
    --patch-operations op='replace',path='/deploymentId',value='<deployment-id>'
```

When deploying an API the first time, you can combine the stage creation and deployment creation at the same time:

```
 aws apigateway create-deployment --region <region> \
    --rest-api-id <rest-api-id> \
    --stage-name <stage-name>
```

This is what is done behind the scenes in the API Gateway console, when you deploy an API the first time or when you redeploy the API to a new stage\.