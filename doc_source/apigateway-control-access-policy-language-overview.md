# Access Policy Language Overview for Amazon API Gateway<a name="apigateway-control-access-policy-language-overview"></a>

The topics in this section describe the basic elements used in Amazon API Gateway resource policies\.

Resource policies are specified using the same syntax as IAM Policies\. For complete policy language information, see [ Overview of IAM Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) and [ AWS Identity and Access Management Policy Reference](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

## Common Elements in an Access Policy<a name="apigateway-common-elements-in-an-access-policy"></a>

In its most basic sense, a resource policy contains the following elements:
+ **Resources** – APIs are the Amazon API Gateway resources for which you can allow or deny permissions\. In a policy, you use the Amazon Resource Name \(ARN\) to identify the resource\.

  For the format of the `Resource` element, see [Resource Format of Permissions for Executing API in API Gateway](api-gateway-control-access-using-iam-policies-to-invoke-api.md#api-gateway-iam-policy-resource-format-for-executing-api)\.
+ **Actions** – For each resource, Amazon API Gateway supports a set of operations\. You identify resource operations you will allow \(or deny\) by using action keywords\.

  For example, the `apigateway:invoke` permission will allow the user permission to invoke an API upon a client request\.

  For the format of the `Action` element, see [Action Format of Permissions for Executing API in API Gateway](api-gateway-control-access-using-iam-policies-to-invoke-api.md#api-gateway-iam-policy-action-format-for-executing-api)\.
+ **Effect** – What the effect will be when the user requests the specific action—this can be either `Allow` or `Deny`\. You can also explicitly deny access to a resource, which you might do in order to make sure that a user cannot access it, even if a different policy grants access\. 
+ **Principal** – The account or user who is allowed access to the actions and resources in the statement\. In a resource policy, the principal is the IAM user or account who is the recipient of this permission\.

The following example resource policy shows the preceding common policy elements\. The policy grants access to a specific API *api\-id* in the specified *region* to any user whose source IP address is in the IP range *203\.0\.113\.0* to *203\.0\.113\.255* only\. The policy denies all access to the API if the user's source IP is not within the range\.

```
{  
    "Id":"Policy1513815114229",
        "Version":"2018-01-01",
        "Statement":[  
            {  
                "Sid":"Stmt1513815112643",
                "Effect":"Deny",
                "Principal":"*",
                "Action":[  
                    "execute-api:Invoke"
                ],
                "Condition":{  
                    "NotIpAddress":{  
                        "aws:SourceIp":[  
                            "203.0.113.0",
                            "203.0.113.255"
                        ]
                    }
                },
                "Resource":[  
                    "arn:aws:execute-api:region:account-id:api-id/stage/method/path"
                ]
            }
        ]
}
```