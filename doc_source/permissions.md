# Control access to an API with IAM permissions<a name="permissions"></a>

 You control access to your Amazon API Gateway API with [IAM permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions.html) by controlling access to the following two API Gateway component processes: 
+  To create, deploy, and manage an API in API Gateway, you must grant the API developer permissions to perform the required actions supported by the API management component of API Gateway\. 
+  To call a deployed API or to refresh the API caching, you must grant the API caller permissions to perform required IAM actions supported by the API execution component of API Gateway\. 

 The access control for the two processes involves different permissions models, explained next\.

## API Gateway permissions model for creating and managing an API<a name="api-gateway-control-access-iam-permissions-model-for-managing-api"></a>

 To allow an API developer to create and manage an API in API Gateway, you must [create IAM permissions policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) that allow a specified API developer to create, update, deploy, view, or delete required [API entities](https://docs.aws.amazon.com/apigateway/api-reference/resource/)\. You attach the permissions policy to an [IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) representing the developer, to an [IAM group](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html) containing the user, or to an [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) assumed by the user\. 

In this IAM policy document, the IAM `Resource` element contains a list of API Gateway API entities, including [API Gateway resources](https://docs.aws.amazon.com/apigateway/api-reference/resource/) and [API Gateway link\-relations](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/)\. The IAM `Action` element contains the required API Gateway API\-managing actions\. These actions are declared in the `apigateway:HTTP_VERB` format, where `apigateway` designates the underlying API management component of API Gateway, and `HTTP_VERB` represents HTTP verbs supported by API Gateway\. 

For more information on how to use this permissions model, see [ Control access for managing an API](api-gateway-control-access-using-iam-policies-to-create-and-manage-api.md)\. 

## API Gateway permissions model for invoking an API<a name="api-gateway-control-access-iam-permissions-model-for-calling-api"></a>

To allow an API caller to invoke the API or refresh its caching, you must create IAM policies that permit a specified API caller to invoke the API method for which the IAM user authentication is enabled\. The API developer sets the method's `authorizationType` property to `AWS_IAM` to require that the caller submit the IAM user's access keys to be authenticated\. Then, you attach the policy to an IAM user representing the API caller, to an IAM group containing the user, or to an IAM role assumed by the user\. 

In this IAM permissions policy statement, the IAM `Resource` element contains a list of deployed API methods identified by given HTTP verbs and API Gateway [resource paths](https://docs.aws.amazon.com/apigateway/api-reference/resource/)\. The IAM `Action` element contains the required API Gateway API executing actions\. These actions include `execute-api:Invoke` or `execute-api:InvalidateCache`, where `execute-api` designates the underlying API execution component of API Gateway\. 

For more information on how to use this permissions model, see [ Control access for invoking an API](api-gateway-control-access-using-iam-policies-to-invoke-api.md)\. 

 When an API is integrated with an AWS service \(for example, AWS Lambda\) in the back end, API Gateway must also have permissions to access integrated AWS resources \(for example, invoking a Lambda function\) on behalf of the API caller\. To grant these permissions, create an IAM role of the **AWS service for API Gateway** type\. When you create this role in the IAM Management console, this resulting role contains the following IAM trust policy that declares API Gateway as a trusted entity permitted to assume the role: 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "apigateway.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

If you create the IAM role by calling the [create\-role](https://docs.aws.amazon.com/cli/latest/reference/iam/create-role.html) command of CLI or a corresponding SDK method, you must supply the above trust policy as the input parameter of `assume-role-policy-document`\. Do not attempt to create such a policy directly in the IAM Management console or calling AWS CLI [create\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/create-policy.html) command or a corresponding SDK method\.

For API Gateway to call the integrated AWS service, you must also attach to this role appropriate IAM permissions policies for calling integrated AWS services\. For example, to call a Lambda function, you must include the following IAM permissions policy in the IAM role: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": "*"
        }
    ]
}
```

Note that Lambda supports resource\-based access policy, which combines both trust and permissions policies\. When integrating an API with a Lambda function using the API Gateway console, you are not asked to set this IAM role explicitly, because the console sets the resource\-based permissions on the Lambda function for you, with your consent\. 

**Note**  
 To enact access control to an AWS service, you can use either the caller\-based permissions model, where a permissions policy is directly attached to the caller's IAM user or group, or the role\-based permission model, where a permissions policy is attached to an IAM role that API Gateway can assume\. The permissions policies may differ in the two models\. For example, the caller\-based policy blocks the access while the role\-based policy allows it\. You can take advantage of this to require that an IAM user access an AWS service through an API Gateway API only\. 