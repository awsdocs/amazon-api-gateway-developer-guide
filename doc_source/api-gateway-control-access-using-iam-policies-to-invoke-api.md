# Control Access for Invoking an API<a name="api-gateway-control-access-using-iam-policies-to-invoke-api"></a>

 In this section you will learn how to write up IAM policy statements to control who can or cannot call a deployed API in API Gateway\. Here, you will also find the policy statement reference, including the formats of `Action` and `Resource` fields related to the API execution service\. 

**Note**  
You should also study the IAM section in [How Amazon API Gateway Resource Policies Affect Authorization Workflow](apigateway-authorization-flow.md)\.

## Control Who Can Call an API Gateway API Method with IAM Policies<a name="api-gateway-who-can-invoke-an-api-method-using-iam-policies"></a>

 To control who can or cannot call a deployed API with IAM permissions, create an IAM policy document with required permissions\. A template for such a policy document is shown as follows\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Permission",
      "Action": [
        "execute-api:Execution-operation"           
      ],
      "Resource": [
        "arn:aws:execute-api:region:account-id:api-id/stage/METHOD_HTTP_VERB/Resource-path"
      ]
    }
  ]
}
```

 Here, `Permission` is to be replaced by `Allow` or `Deny` depending on whether you want to grant or revoke the included permissions\. `Execution-operation` is to be replaced by the operations supported by the API execution service\. `METHOD_HTTP_VERB` stands for a HTTP verb supported by the specified resources\. `Resource-path` is the placeholder for the URL path of a deployed API `[Resource](https://docs.aws.amazon.com/apigateway/api-reference/resource/resource/)` instance supporting the said `METHOD_HTTP-VERB`\. For more information, see [Statement Reference of IAM Policies for Executing API in API Gateway](#api-gateway-calling-api-permissions)\. 

**Note**  
For IAM policies to be effective, you must have enabled IAM authentication on API methods by setting `AWS_IAM` for the methods' `[authorizationType](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/#authorizationType)` property\. Failing to do so will make these API methods publicly accessible\.

 For example, to grant a user the permission to view a list of pets exposed by a specified API, but to deny the user the permission to add a pet to the list, you could include the following statement in the IAM policy: 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "execute-api:Invoke"           
      ],
      "Resource": [
        "arn:aws:execute-api:us-east-1:account-id:api-id/*/GET/pets"
      ]
    },
    {
      "Effect": "Deny",
      "Action": [
        "execute-api:Invoke"           
      ],
      "Resource": [
        "arn:aws:execute-api:us-east-1:account-id:api-id/*/POST/pets"
      ]
    }
  ]
}
```

 For a developer team testing APIs, you can include the following statement in the IAM policy to allow the team to call any method on any resource of any API by any developer in the `test` stage\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "execute-api:Invoke",
        "execute-api:InvalidateCache"
      ],
      "Resource": [
        "arn:aws:execute-api:*:*:*/test/*"
      ]
    }
  ]
}
```

## Statement Reference of IAM Policies for Executing API in API Gateway<a name="api-gateway-calling-api-permissions"></a>

The following information describes the Action and Resource format of IAM policy statements of access permissions for executing an API\.

### Action Format of Permissions for Executing API in API Gateway<a name="api-gateway-iam-policy-action-format-for-executing-api"></a>

The API\-executing `Action` expression has the following general format:

```
execute-api:action
```

where *action* is an available API\-executing action:
+ **\***, which represents all of the following actions\.
+ **Invoke**, used to invoke an API upon a client request\.
+ **InvalidateCache**, used to invalidate API cache upon a client request\.

### Resource Format of Permissions for Executing API in API Gateway<a name="api-gateway-iam-policy-resource-format-for-executing-api"></a>

The API\-executing `Resource` expression has the following general format:

```
arn:aws:execute-api:region:account-id:api-id/stage-name/HTTP-VERB/resource-path-specifier
```

where:
+ *region* is the AWS region \(such as **us\-east\-1** or **\*** for all AWS regions\) that corresponds to the deployed API for the method\.
+ *account\-id* is the 12\-digit AWS account Id of the REST API owner\. 
+ *api\-id* is the identifier API Gateway has assigned to the API for the method\. \(**\*** can be used for all APIs, regardless of the API's identifier\.\)
+ *stage\-name* is the name of the stage associated with the method \(**\*** can be used for all stages, regardless of the stage's name\.\)
+ *HTTP\-VERB* is the HTTP verb for the method\. It can be one of the following: GET, POST, PUT, DELETE, PATCH, HEAD, OPTIONS\. 
+ *resource\-path\-specifier* is the path to the desired method\. \(**\*** can be used for all paths\)\.

Some example resource expressions include:
+ **arn:aws:execute\-api:\*:\*:\*** for any resource path in any stage, for any API in any AWS region\. \(This is equivalent to `*`\)\.
+ **arn:aws:execute\-api:us\-east\-1:\*:\*** for any resource path in any stage, for any API in the AWS region of `us-east-1`\.
+ **arn:aws:execute\-api:us\-east\-1:\*:*api\-id*/\*** for any resource path in any stage, for the API with the identifier of *api\-id* in the AWS region of us\-east\-1\.
+ **arn:aws:execute\-api:us\-east\-1:\*:*api\-id*/`test`/\*** for resource path in the stage of `test`, for the API with the identifier of *api\-id* in the AWS region of us\-east\-1\.
+ **arn:aws:execute\-api:us\-east\-1:\*:*api\-id*/`test`/\*/mydemoresource/\*** for any resource path along the path of `mydemoresource`, for any HTTP method in the stage of `test`, for the API with the identifier of *api\-id* in the AWS region of us\-east\-1\.
+ **arn:aws:execute\-api:us\-east\-1:\*:*api\-id*/`test`/GET/mydemoresource/\*** for GET methods under any resource path along the path of `mydemoresource`, in the stage of `test`, for the API with the identifier of *api\-id* in the AWS region of us\-east\-1\. 