# API Gateway Resource Policy Examples<a name="apigateway-resource-policies-examples"></a>

This section presents a few examples of typical use cases for API Gateway resource policies\. The policies use *account\-id* and *api\-id* strings in the resource value\. To test these policies, you need to replace these strings with your own account ID and API ID\. For information about access policy language, see [Access Policy Language Overview for Amazon API Gateway](apigateway-control-access-policy-language-overview.md)\.

**Topics**
+ [Example: Allow users in another AWS account to use an API](#apigateway-resource-policies-cross-account-example)
+ [Example: Deny API traffic based on source IP address or range](#apigateway-resource-policies-source-ip-address-example)
+ [Example: Allow private API traffic based on source VPC or VPC endpoint](#apigateway-resource-policies-source-vpc-example)

## Example: Allow users in another AWS account to use an API<a name="apigateway-resource-policies-cross-account-example"></a>

The following example resource policy grants access to two users in an AWS account via [Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/sigv4_signing.html) \(SigV4\) protocols\. Specifically, Alice and the root user for the same AWS account are granted the `execute-api:Invoke` action to execute the `GET` action on the `pets` resource \(API\)\.

```
{
   "Version": "2012-10-17",
   "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::account-id:user/Alice",
                    "account-id"
                ]
            },
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:region:account-id:api-id/stage/GET/pets"
            ]
        }
    ]
}
```

## Example: Deny API traffic based on source IP address or range<a name="apigateway-resource-policies-source-ip-address-example"></a>

The following example resource policy is a "blacklist" policy that denies \(blocks\) incoming traffic to an API from two specified source IP addresses\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:region:account-id:api-id/*"
            ]
        },
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
               "arn:aws:execute-api:region:account-id:api-id/*"
            ],
            "Condition" : {
                "IpAddress": {
                    "aws:SourceIp": ["192.0.2.0/24", "198.51.100.0/24" ]
                }
            }
        }
    ]
}
```

## Example: Allow private API traffic based on source VPC or VPC endpoint<a name="apigateway-resource-policies-source-vpc-example"></a>

The following example resource policies allow incoming traffic to a private API only from a specified virtual private cloud \(VPC\) or VPC endpoint\.

This example resource policy specifies a source VPC:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:region:account-id:api-id/*"
            ]
        },
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:region:account-id:api-id/*"
            ],
            "Condition" : {
                "StringNotEquals": {
                   "aws:SourceVpc": "vpc-2f09a348"
                }
            }
        }
    ]
}
```

This example resource policy specifies a source VPC endpoint:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:region:account-id:api-id/*"
            ]
        },
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:region:account-id:api-id/*"
            ],
            "Condition" : {
                "StringNotEquals": {
                    "aws:SourceVpce": "vpce-1a2b3c4d"
                }
            }
        }
    ]
}
```