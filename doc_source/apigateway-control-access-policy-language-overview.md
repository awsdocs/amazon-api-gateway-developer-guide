# Access policy language overview for Amazon API Gateway<a name="apigateway-control-access-policy-language-overview"></a>

This page describes the basic elements used in Amazon API Gateway resource policies\.

Resource policies are specified using the same syntax as IAM policies\. For complete policy language information, see [ Overview of IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) and [ AWS Identity and Access Management Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

For information about how an AWS service decides whether a given request should be allowed or denied, see [Determining Whether a Request is Allowed or Denied](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html#policy-eval-denyallow)\.

## Common elements in an access policy<a name="apigateway-common-elements-in-an-access-policy"></a>

In its most basic sense, a resource policy contains the following elements:
+ **Resources** – APIs are the Amazon API Gateway resources for which you can allow or deny permissions\. In a policy, you use the Amazon Resource Name \(ARN\) to identify the resource\. You can also use abbreviated syntax, which API Gateway automatically expands to the full ARN when you save a resource policy\. To learn more, see [API Gateway resource policy examples](apigateway-resource-policies-examples.md)\.

  For the format of the full `Resource` element, see [Resource format of permissions for executing API in API Gateway](api-gateway-control-access-using-iam-policies-to-invoke-api.md#api-gateway-iam-policy-resource-format-for-executing-api)\.
+ **Actions** – For each resource, Amazon API Gateway supports a set of operations\. You identify resource operations that you will allow \(or deny\) by using action keywords\.

  For example, the `apigateway:invoke` permission will allow the user permission to invoke an API upon a client request\.

  For the format of the `Action` element, see [Action format of permissions for executing API in API Gateway](api-gateway-control-access-using-iam-policies-to-invoke-api.md#api-gateway-iam-policy-action-format-for-executing-api)\.
+ **Effect** – What the effect is when the user requests the specific action—this can be either `Allow` or `Deny`\. You can also explicitly deny access to a resource, which you might do in order to make sure that a user cannot access it, even if a different policy grants access\. 
**Note**  
"Implicit deny" is the same thing as "deny by default"\.  
An "implicit deny" is different from an "explicit deny"\. For more information, see [The Difference Between Denying by Default and Explicit Deny](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html#AccessPolicyLanguage_Interplay)\.
+ **Principal** – The account or user who is allowed access to the actions and resources in the statement\. In a resource policy, the principal is the IAM user or account who is the recipient of this permission\.

The following example resource policy shows the preceding common policy elements\. The policy grants access to the API under the specified *account\-id* in the specified *region* to any user whose source IP address is in the address block *123\.4\.5\.6/24*\. The policy denies all access to the API if the user's source IP is not within the range\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": "arn:aws:execute-api:region:account-id:*"
        },
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": "arn:aws:execute-api:region:account-id:*",
            "Condition": {
                "NotIpAddress": {
                    "aws:SourceIp": "123.4.5.6/24"
                }
            }
        }
    ]
}
```