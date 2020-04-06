# Deploying a REST API in Amazon API Gateway<a name="how-to-deploy-api"></a>

 After creating your API, you must deploy it to make it callable by your users\. 

To deploy an API, you create an API deployment and associate it with a stage\. A stage is a logical reference to a lifecycle state of your API \(for example, `dev`, `prod`, `beta`, `v2`\)\. API stages are identified by the API ID and stage name\. They're included in the URL that you use to invoke the API\. Each stage is a named reference to a deployment of the API and is made available for client applications to call\. 

**Important**  
Every time you update an API, you must redeploy the API to an existing stage or to a new stage\. Updating an API includes modifying routes, methods, integrations, authorizers, and anything else other than stage settings\. 

As your API evolves, you can continue to deploy it to different stages as different versions of the API\. You can also deploy your API updates as a [canary release deployment](canary-release.md)\. This enables your API clients to access, on the same stage, the production version through the production release, and the updated version through the canary release\. 

To call a deployed API, the client submits a request against an API's URL\. The URL is determined by an API's protocol \(HTTP\(S\) or \(WSS\)\), hostname, stage name, and \(for REST APIs\) resource path\. The hostname and the stage name determine the API's base URL\. 

Using the API's default domain name, the base URL of a REST API \(for example\) in a given stage \(`{stageName}`\) is in the following format:

```
https://{restapi-id}.execute-api.{region}.amazonaws.com/{stageName}
```

 To make the API's default base URL more user\-friendly, you can create a custom domain name \(for example, `api.example.com`\) to replace the default hostname of the API\. To support multiple APIs under the custom domain name, you must map an API stage to a base path\. 

With a custom domain name of `{api.example.com}` and the API stage mapped to a base path of \(`{basePath}`\) under the custom domain name, the base URL of a REST API becomes the following: 

```
https://{api.example.com}/{basePath}
```

 For each stage, you can optimize API performance by adjusting the default account\-level request throttling limits and enabling API caching\. You can also enable logging for API calls to CloudTrail or CloudWatch, and can select a client certificate for the backend to authenticate the API requests\. In addition, you can override stage\-level settings for individual methods and define stage variables to pass stage\-specific environment contexts to the API integration at runtime\. 

Stages enable robust version control of your API\. For example, you can deploy an API to a `test` stage and a `prod` stage, and use the `test` stage as a test build and use the `prod` stage as a stable build\. After the updates pass the test, you can promote the `test` stage to the `prod` stage\. The promotion can be done by redeploying the API to the `prod` stage or updating a [stage variable](how-to-deploy-api-with-console.md#how-to-deploy-api-set-stage-variables) value from the stage name of `test` to that of `prod`\.

 In this section, we discuss how to deploy an API by using the [API Gateway console](https://console.aws.amazon.com/apigateway) or calling the [API Gateway REST API](https://docs.aws.amazon.com/apigateway/api-reference/)\. To use other tools, see the documentation of the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/apigateway) or an [AWS SDK](https://aws.amazon.com/tools/#sdk)\. 

**Topics**
+ [Deploy a REST API in API Gateway](set-up-deployments.md)
+ [Setting up a stage for a REST API](set-up-stages.md)
+ [Set up an API Gateway canary release deployment](canary-release.md)
+ [Updates to a REST API that require redeployment](updating-api.md)