# Create and Attach an API Gateway Resource Policy to an API<a name="apigateway-resource-policies-create-attach"></a>

To allow a user to access your API by calling the API execution service, you must create an API Gateway resource policy, which controls access to the API Gateway resources, and attach the policy to the API\.

The resource policy can be attached to the API when the API is created, or it can be updated later\.

**Important**  
If you update the resource policy after the API is created, you'll need to deploy the API to propagate the changes after you've attached the updated policy\. Updating or saving the policy alone won't change the runtime behavior of the API\. For more information about deploying your API, see [Deploying an API in Amazon API Gateway](how-to-deploy-api.md)\.

Access can be controlled by IAM condition elements, including conditions on AWS account or IP range\. If the `Principal` in the policy is set to `"*"`, other authorization types can be used alongside the resource policy\. If the `Principal` is set to `"AWS": ...`, authorization will fail for all resources not secured with `AWS_IAM` authorization, including unsecured resources\.

The following sections describe how to create your own API Gateway resource policy and attach it to your API\. Attaching a policy applies the permissions in the policy to the methods in the API\.

## Attaching API Gateway Resource Policies \(Console\)<a name="apigateway-resource-policies-create-attach-console"></a>

You can use the AWS Management console to attach a resource policy to an API Gateway API\. 

**To attach a resource policy to an API Gateway API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose the API name\.

1. In the left\-hand navigation pane, choose **Resource Policy**\.

1. If desired, choose one of the **Examples**\. In the example policies, placeholders are enclosed in double curly braces \(`"{{}}"`\)\. Replace each of the placeholders \(including the curly braces\) with the necessary information\.

   If you don't use one of the **Examples**, enter your resource policy\.

1. Choose **Save**\.

## Attaching API Gateway Resource Policies \(AWS CLI\)<a name="apigateway-resource-policies-create-attach-using-cli"></a>

To use the AWS CLI to create a new API and attach a resource policy to it, call the [http://docs.aws.amazon.com/cli/latest/reference/apigateway/create-rest-api.html](http://docs.aws.amazon.com/cli/latest/reference/apigateway/create-rest-api.html) command as follows:

```
aws apigateway create-rest-api \
    --name "api-name" \
    --policy "{\"jsonEscapedPolicyDocument\"}"
```

To use the AWS CLI to attach a resource policy to an existing API, call the [http://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html](http://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html) command as follows: 

```
aws apigateway update-rest-api \
    --rest-api-id api-id \
    --patch-operations op=replace,path=/policy,value='{\"jsonEscapedPolicyDocument\"}'
```

## Attaching API Gateway Resource Policies \(API Gateway API\)<a name="apigateway-resource-policies-create-attach-using-api"></a>

To use the [API Gateway REST API](http://docs.aws.amazon.com/apigateway/api-reference/) to create a new API and attach a resource policy to it, call the [http://docs.aws.amazon.com/cli/latest/reference/apigateway/create-rest-api.html](http://docs.aws.amazon.com/cli/latest/reference/apigateway/create-rest-api.html) command as follows:

```
POST /restapis

{
 "name": "api-name",
 "policy": "{\"jsonEscapedPolicyDocument\"}"
}
// simplified format supported here because apiId is not known yet and partition/region/account can be derived at import time
// "Resource": [
//   "execute-api:/stage/method/path" 
// ]
```

To use the [API Gateway REST API](http://docs.aws.amazon.com/apigateway/api-reference/) to attach a resource policy to an existing API, call the [http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/) command as follows: 

```
PATCH /restapis/api-id

{
 "patchOperations" : [ {
   "op": "replace",
   "path": "/policy",
   "value": "{\"jsonEscapedPolicyDocument\"}"
  } ]
}
```

## Swagger Example of Attaching a API Gateway Resource Policy<a name="apigateway-resource-policies-create-attach-using-swagger"></a>

The [http://docs.aws.amazon.com/apigateway/api-reference/link-relation/import-restapi/](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/import-restapi/) command can be used to import a Swagger definition of an API with attached resource policy, as shown in the following example:

```
{
 "swagger": "2.0",
 "x-amazon-apigateway-policy": {
   "Version": "2012-10-17",
   "Statement": [
      {
       "Effect": "Allow",
       "Principal": {
         "AWS": [
           "arn:aws:iam::111122223333:user/Alice",
           "arn:aws:iam::111122223333:root"
          ]
        },
       "Action": "execute-api:Invoke",
       "Resource": [
         "execute-api:/stage/method/path" // simplified format supported here because apiId is not known yet and partition/region/account can derived at import time
       ]
      }
    ]
  },
 "info" : {
   "title" : "Example"
  },
 "schemes": [
   "https"
  ],
 "paths": {...}
}
```