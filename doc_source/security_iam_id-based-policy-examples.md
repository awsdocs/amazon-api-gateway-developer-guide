# Amazon API Gateway identity\-based policy examples<a name="security_iam_id-based-policy-examples"></a>

By default, IAM users and roles don't have permission to create or modify API Gateway resources\. They also can't perform tasks using the AWS Management Console, AWS CLI, or AWS SDKs\. An IAM administrator must create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. The administrator must then attach those policies to the IAM users or groups that require those permissions\.

For information about how to create IAM policies, see [Creating Policies on the JSON Tab](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-json-editor) in the *IAM User Guide*\.

**Topics**
+ [Policy best practices](#security_iam_service-with-iam-policy-best-practices)
+ [Allow users to view their own permissions](#security_iam_id-based-policy-examples-view-own-permissions)
+ [Simple read permissions](#api-gateway-policy-example-apigateway-general)
+ [Create only REQUEST or JWT authorizers](#security_iam_id-based-policy-examples-v2-import)
+ [Require that the default `execute-api` endpoint is disabled](#security_iam_id-based-policy-examples-v2-endpoint-status)
+ [Allow users to create or update only private REST APIs](#security_iam_id-based-policy-examples-private-api)
+ [Require that API routes have authorization](#security_iam_id-based-policy-examples-require-authorization)
+ [Prevent a user from creating or updating a VPC link](#security_iam_id-based-policy-examples-deny-vpc-link)

## Policy best practices<a name="security_iam_service-with-iam-policy-best-practices"></a>

Identity\-based policies are very powerful\. They determine whether someone can create, access, or delete API Gateway resources in your account\. These actions can incur costs for your AWS account\. When you create or edit identity\-based policies, follow these guidelines and recommendations:
+ **Get started using AWS managed policies** – To start using API Gateway quickly, use AWS managed policies to give your employees the permissions they need\. These policies are already available in your account and are maintained and updated by AWS\. For more information, see [Get started using permissions with AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#bp-use-aws-defined-policies) in the *IAM User Guide*\.
+ **Grant least privilege** – When you create custom policies, grant only the permissions required to perform a task\. Start with a minimum set of permissions and grant additional permissions as necessary\. Doing so is more secure than starting with permissions that are too lenient and then trying to tighten them later\. For more information, see [Grant least privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege) in the *IAM User Guide*\.
+ **Enable MFA for sensitive operations** – For extra security, require IAM users to use multi\-factor authentication \(MFA\) to access sensitive resources or API operations\. For more information, see [Using multi\-factor authentication \(MFA\) in AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) in the *IAM User Guide*\.
+ **Use policy conditions for extra security** – To the extent that it's practical, define the conditions under which your identity\-based policies allow access to a resource\. For example, you can write conditions to specify a range of allowable IP addresses that a request must come from\. You can also write conditions to allow requests only within a specified date or time range, or to require the use of SSL or MFA\. For more information, see [IAM JSON policy elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

## Allow users to view their own permissions<a name="security_iam_id-based-policy-examples-view-own-permissions"></a>

This example shows how you might create a policy that allows IAM users to view the inline and managed policies that are attached to their user identity\. This policy includes permissions to complete this action on the console or programmatically using the AWS CLI or AWS API\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ViewOwnUserInfo",
            "Effect": "Allow",
            "Action": [
                "iam:GetUserPolicy",
                "iam:ListGroupsForUser",
                "iam:ListAttachedUserPolicies",
                "iam:ListUserPolicies",
                "iam:GetUser"
            ],
            "Resource": ["arn:aws:iam::*:user/${aws:username}"]
        },
        {
            "Sid": "NavigateInConsole",
            "Effect": "Allow",
            "Action": [
                "iam:GetGroupPolicy",
                "iam:GetPolicyVersion",
                "iam:GetPolicy",
                "iam:ListAttachedGroupPolicies",
                "iam:ListGroupPolicies",
                "iam:ListPolicyVersions",
                "iam:ListPolicies",
                "iam:ListUsers"
            ],
            "Resource": "*"
        }
    ]
}
```

## Simple read permissions<a name="api-gateway-policy-example-apigateway-general"></a>

This example policy gives a user permission to get information about all of the resources of an HTTP or WebSocket API with the identifier of `a123456789` in the AWS Region of us\-east\-1\. The resource `arn:aws:apigateway:us-east-1::/apis/a123456789/*` includes all sub\-resources of the API such as authorizers and deployments\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "apigateway:GET"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/apis/a123456789/*"
      ]
    }
  ]
}
```

## Create only REQUEST or JWT authorizers<a name="security_iam_id-based-policy-examples-v2-import"></a>

This example policy allows a user to create APIs with only `REQUEST` or `JWT` authorizers, including through [import](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis.html#ImportApi)\. In the `Resource` section of the policy, `arn:aws:apigateway:us-east-1::/apis/??????????` requires that resources have a maximum of 10 characters, which excludes sub\-resources of an API\. This example uses `ForAllValues` in the `Condition` section because users can create multiple authorizers at once by importing an API\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "OnlyAllowSomeAuthorizerTypes",
      "Effect": "Allow",
      "Action": [
        "apigateway:PUT",
        "apigateway:POST",
        "apigateway:PATCH"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/apis",
        "arn:aws:apigateway:us-east-1::/apis/??????????",
        "arn:aws:apigateway:us-east-1::/apis/*/authorizers",
        "arn:aws:apigateway:us-east-1::/apis/*/authorizers/*"
      ],
      "Condition": {
        "ForAllValues:StringEqualsIfExists": {
          "apigateway:Request/AuthorizerType": [
            "REQUEST",
            "JWT"
          ]
        }
      }
    }
  ]
}
```

## Require that the default `execute-api` endpoint is disabled<a name="security_iam_id-based-policy-examples-v2-endpoint-status"></a>

 This example policy allows users to create, update or import an API, with the requirement that `DisableExecuteApiEndpoint` is `true`\. When `DisableExecuteApiEndpoint` is `true`, clients can't use the default `execute-api` endpoint to invoke an API\.

We use the `BoolIfExisits` condition to handle a call to update an API that doesn't have the `DisableExecuteApiEndpoint` condition key populated\. When a user attempts to create or import an API, the `DisableExecuteApiEndpoint` condition key is always populated\.

Because the `apis/*` resource also captures sub resources such as authorizers or methods, we explicitly scope it to just APIs with a `Deny` statement\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DisableExecuteApiEndpoint",
      "Effect": "Allow",
      "Action": [
        "apigateway:PATCH",
        "apigateway:POST",
        "apigateway:PUT"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/apis",
        "arn:aws:apigateway:us-east-1::/apis/*"
      ],
      "Condition": {
        "BoolIfExists": {
          "apigateway:Request/DisableExecuteApiEndpoint": true
        }
      }
    },
    {
      "Sid": "ScopeDownToJustApis",
      "Effect": "Deny",
      "Action": [
        "apigateway:PATCH",
        "apigateway:POST",
        "apigateway:PUT"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/apis/*/*"
      ]
    }
  ]
}
```

## Allow users to create or update only private REST APIs<a name="security_iam_id-based-policy-examples-private-api"></a>

This example policy uses condition keys to require that a user creates only `PRIVATE` APIs, and to prevent updates that might change an API from `PRIVATE` to another type, such as `REGIONAL`\.

We use `ForAllValues` to require that every `EndpointType` added to an API is `PRIVATE`\. We use a resource condition key to allow any update to an API as long as it's `PRIVATE`\. `ForAllValues` applies only if a condition key is present\. For create operations, a Resource condition key is not present\. For delete operations, a Request key is not present\.

We use the non\-greedy matcher \(`?`\) to explicitly match against API IDs to prevent allowing non\-API resources such as authorizers\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ScopeToPrivateApis",
      "Effect": "Allow",
      "Action": [
        "apigateway:DELETE",
        "apigateway:PATCH",
        "apigateway:POST",
        "apigateway:PUT"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/restapis",
        "arn:aws:apigateway:us-east-1::/restapis/??????????"
      ],
      "Condition": {
        "ForAllValues:StringEqualsIfExists": {
          "apigateway:Request/EndpointType": "PRIVATE",
          "apigateway:Resource/EndpointType": "PRIVATE"
        }
      }
    },
    {
      "Sid": "AllowResourcePolicyUpdates",
      "Effect": "Allow",
      "Action": [
          "apigateway:UpdateRestApiPolicy"
      ],
      "Resource": [
          "arn:aws:apigateway:us-east-1::/restapis/*"
      ]
    }
  ]
}
```

## Require that API routes have authorization<a name="security_iam_id-based-policy-examples-require-authorization"></a>

This policy causes attempts to create or update a route \(including through [import](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis.html#ImportApi)\) to fail if the route has no authorization\. `ForAnyValue` evaluates to false if the key is not present, such as when a route is not being created or updated\. We use `ForAnyValue` because multiple routes can be created through import\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowUpdatesOnApisAndRoutes",
      "Effect": "Allow",
      "Action": [
        "apigateway:POST",
        "apigateway:PATCH",
        "apigateway:PUT"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/apis",
        "arn:aws:apigateway:us-east-1::/apis/??????????",
        "arn:aws:apigateway:us-east-1::/apis/*/routes",
        "arn:aws:apigateway:us-east-1::/apis/*/routes/*"
      ]
    },
    {
      "Sid": "DenyUnauthorizedRoutes",
      "Effect": "Deny",
      "Action": [
        "apigateway:POST",
        "apigateway:PATCH",
        "apigateway:PUT"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/apis",
        "arn:aws:apigateway:us-east-1::/apis/*"
      ],
      "Condition": {
        "ForAnyValue:StringEqualsIgnoreCase": {
          "apigateway:Request/RouteAuthorizationType": "NONE"
        }
      }
    }
  ]
}
```

## Prevent a user from creating or updating a VPC link<a name="security_iam_id-based-policy-examples-deny-vpc-link"></a>

This policy prevents a user from creating or updating a VPC link\. A VPC link enables you to expose resources within an Amazon VPC to clients outside of the VPC\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyVPCLink",
      "Effect": "Deny",
      "Action": [
        "apigateway:POST",
        "apigateway:PUT",
        "apigateway:PATCH"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/vpclinks",
        "arn:aws:apigateway:us-east-1::/vpclinks/*"
      ]
    }
  ]
}
```