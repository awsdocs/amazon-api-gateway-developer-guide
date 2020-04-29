# Create and attach an API Gateway resource policy to an API<a name="apigateway-resource-policies-create-attach"></a>

To allow a user to access your API by calling the API execution service, you must create an API Gateway resource policy, which controls access to the API Gateway resources, and attach the policy to the API\.

**Important**  
To update an API Gateway resource policy, you'll need to have `apigateway:UpdateRestApiPolicy` permission in addition to `apigateway:PATCH` permission\. 

The resource policy can be attached to the API when the API is being created, or it can be attached afterwards\. For private APIs, note that until you attach the resource policy to the private API, all calls to the API will fail\.

**Important**  
If you update the resource policy after the API is created, you'll need to deploy the API to propagate the changes after you've attached the updated policy\. Updating or saving the policy alone won't change the runtime behavior of the API\. For more information about deploying your API, see [Deploying a REST API in Amazon API Gateway](how-to-deploy-api.md)\.

Access can be controlled by IAM condition elements, including conditions on AWS account, source VPC, source VPC endpoint, or IP range\. If the `Principal` in the policy is set to `"*"`, other authorization types can be used alongside the resource policy\. If the `Principal` is set to `"AWS"`, authorization will fail for all resources not secured with `AWS_IAM` authorization, including unsecured resources\.

The following sections describe how to create your own API Gateway resource policy and attach it to your API\. Attaching a policy applies the permissions in the policy to the methods in the API\.

**Important**  
If you use the API Gateway console to attach a resource policy to a deployed API, or if you update an existing resource policy, you'll need to redeploy the API in the console for the changes to take effect\.

**Topics**
+ [Attaching API Gateway resource policies \(console\)](#apigateway-resource-policies-create-attach-console)
+ [Attaching API Gateway resource policies \(AWS CLI\)](#apigateway-resource-policies-create-attach-using-cli)

## Attaching API Gateway resource policies \(console\)<a name="apigateway-resource-policies-create-attach-console"></a>

You can use the AWS Management console to attach a resource policy to an API Gateway API\. 

**To attach a resource policy to an API Gateway API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose a REST API\.

1. In the left navigation pane, choose **Resource Policy**\.

1. If desired, choose one of the **Examples**\. In the example policies, placeholders are enclosed in double curly braces \(`"{{placeholder}}"`\)\. Replace each of the placeholders \(including the curly braces\) with the necessary information\.

   If you don't use one of the **Examples**, enter your resource policy\.

1. Choose **Save**\.

If the API has been deployed previously in the API Gateway console, you'll need to redeploy it for the resource policy to take effect\.

## Attaching API Gateway resource policies \(AWS CLI\)<a name="apigateway-resource-policies-create-attach-using-cli"></a>

To use the AWS CLI to create a new API and attach a resource policy to it, call the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/create-rest-api.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/create-rest-api.html) command as follows:

```
aws apigateway create-rest-api \
    --name "api-name" \
    --policy "{\"jsonEscapedPolicyDocument\"}"
```

To use the AWS CLI to attach a resource policy to an existing API, call the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html) command as follows: 

```
aws apigateway update-rest-api \
    --rest-api-id api-id \
    --patch-operations op=replace,path=/policy,value='{\"jsonEscapedPolicyDocument\"}'
```