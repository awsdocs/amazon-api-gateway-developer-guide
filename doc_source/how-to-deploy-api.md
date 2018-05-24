# Deploying an API in Amazon API Gateway<a name="how-to-deploy-api"></a>

 After creating your API, you must deploy it to make the API callable for your users\. 

To deploy an API, you create an API [deployment](http://docs.aws.amazon.com/apigateway/api-reference/resource/deployment/) and associate it with a [stage](http://docs.aws.amazon.com/apigateway/api-reference/resource/stage/)\. Each stage is a snapshot of the API and is made available for the client to call\. Every time you update an API, which includes modification of methods, integrations, authorizers, and anything else other than stage settings, you must redeploy the API to an existing stage or to a new stage\. As your API evolves, you can continue to deploy it to different stages as different versions of the API\. You can also deploy your API updates as a [canary release deployment](canary-release.md), enabling your API clients to access, on the same stage, the production version through the production release, and the updated version through the canary release\. 

To call a deployed API, the client submits a request against an API method URL\. The method URL is determined by an API's host name, a stage name, and a resource path\. The host name and the stage name determine the API's base URL\. 

Using the API's default domain name, the base URL of an API in a given stage \(*`{stageName}`*\) is of the following format:

```
https://{restapi-id}.execute-api.{region}.amazonaws.com/{stageName}
```

 To make an API's default base URL more user\-friendly, you can create a custom domain name \(e\.g\., `api.example.com`\) to replace the default host name of the API\. To support multiple APIs under the custom domain name, you must map an API stage to a base path\. 

With a custom domain name of `{api.example.com}` and the API stage mapped to a base path of \(`{basePath}`\) under the custom domain name, the base URL becomes the following: 

```
https://{api.example.com}/{basePath}
```

 For each stage, you can optimize the API performance by adjusting the default account\-level request throttling limits and enabling API caching\. You can also enable logging API calls to CloudTrail or CloudWatch and select a client certificate for the backend to authenticate the API requests\. In addition, you can override stage\-level settings for individual methods and define stage variables to pass stage\-specific environment contexts to the API integration at run time\. At an API stage, you can export the API definitions and generate an SDK for your users to call the API using a supported programming language\. 

Stages enable robust version control of your API\. For example, you can deploy an API to a `test` stage and a `prod` stage, and use the `test` stage as a test build and use the `prod` stage as a stable build\. After the updates pass the test, you can promote the `test` stage to the `prod` stage\. The promotion can be done by redeploying the API to the prod stage or updating a [stage variable](how-to-deploy-api-with-console.md#how-to-deploy-api-set-stage-variables) value from the stage name of `test` to that of `prod`\.

You can also include a [canary release](https://martinfowler.com/bliki/CanaryRelease.html) for testing new changes\. This is referred to as a canary release deployment\. It makes available a base version and updated versions of the API on the same stage, allowing you to introduce new features in the same environment for the base version\. For more information, see [Set up an API Gateway Canary Release Deployment](canary-release.md)\.

 In this section, we discuss how to deploy an API, using the [API Gateway console](https://console.aws.amazon.com/apigateway) or calling the [API Gateway REST API](http://docs.aws.amazon.com/apigateway/api-reference/)\. To use other tools to do the same, see the documentation of, for example, [AWS CLI](http://docs.aws.amazon.com/cli/latest/reference/apigateway) or an [AWS SDK](https://aws.amazon.com/tools/#sdk)\. 

 To monetize your API deployment, you can leverage the API Gateway integration with AWS Marketplace to vend your API as a Software as a Service \(SaaS\) product\. The instructions are also included in this chapter\. 

**Topics**
+ [Create a Deployment in API Gateway](set-up-deployments.md)
+ [Set up a Stage in API Gateway](set-up-stages.md)
+ [Set up an API Gateway Canary Release Deployment](canary-release.md)
+ [Export an API from API Gateway](api-gateway-export-api.md)
+ [Generate SDK of an API](how-to-generate-sdk.md)
+ [Set up Custom Domain Name for an API in API Gateway](how-to-custom-domains.md)
+ [Sell Your API Gateway API through AWS Marketplace](sell-api-as-saas-on-aws-marketplace.md)