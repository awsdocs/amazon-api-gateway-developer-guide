# API Gateway resource policy examples<a name="apigateway-resource-policies-examples"></a>

This page presents a few examples of typical use cases for API Gateway resource policies\.

The following example policies use a simplified syntax to specify the API resource\. This simplified syntax is an abbreviated way that you can refer to an API resource, instead of specifying the full Amazon Resource Name \(ARN\)\. API Gateway converts the abbreviated syntax to the full ARN when you save the policy\. For example, you can specify the resource `execute-api:/stage-name/GET/pets` in a resource policy\. API Gateway converts the resource to `arn:aws:execute-api:us-east-2:123456789012:aabbccddee/stage-name/GET/pets` when you save the resource policy\. API Gateway builds the full ARN by using the current Region, your AWS account ID, and the ID of the REST API that the resource policy is associated with\. You can use `execute-api:/*` to represent all stages, methods, and paths in the current API\. For information about access policy language, see [Access policy language overview for Amazon API Gateway](apigateway-control-access-policy-language-overview.md)\.

**Topics**
+ [Example: Allow users in another AWS account to use an API](#apigateway-resource-policies-cross-account-example)
+ [Example: Deny API traffic based on source IP address or range](#apigateway-resource-policies-source-ip-address-example)
+ [Example: Deny API traffic based on source IP address or range when using a private API](#apigateway-resource-policies-source-ip-address-vpc-example)
+ [Example: Allow private API traffic based on source VPC or VPC endpoint](#apigateway-resource-policies-source-vpc-example)

## Example: Allow users in another AWS account to use an API<a name="apigateway-resource-policies-cross-account-example"></a>

The following example resource policy grants API access in one AWS account to two users in a different AWS account via [Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/sigv4_signing.html) \(SigV4\) protocols\. Specifically, Alice and the root user for the AWS account identified by `account-id-2` are granted the `execute-api:Invoke` action to execute the `GET` action on the `pets` resource \(API\) in your AWS account\.

```
{
   "Version": "2012-10-17",
   "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::account-id-2:user/Alice",
                    "account-id-2"
                ]
            },
            "Action": "execute-api:Invoke",
            "Resource": [
                "execute-api:/stage/GET/pets"
            ]
        }
    ]
}
```

## Example: Deny API traffic based on source IP address or range<a name="apigateway-resource-policies-source-ip-address-example"></a>

The following example resource policy is a "blacklist" policy that denies \(blocks\) incoming traffic to an API from two specified source IP address blocks\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
                "execute-api:/*"
            ]
        },
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
               "execute-api:/*"
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

## Example: Deny API traffic based on source IP address or range when using a private API<a name="apigateway-resource-policies-source-ip-address-vpc-example"></a>

The following example resource policy denies \(blocks\) incoming traffic to a private API from two specified source IP address blocks\. When using private APIs, the VPC endpoint for `execute-api` re\-writes the original source IP address\. The `aws:VpcSourceIp` condition filters the request against the original requester IP address\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
                "execute-api:/*"
            ]
        },
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
               "execute-api:/*"
            ],
            "Condition" : {
                "IpAddress": {
                    "aws:VpcSourceIp": ["192.0.2.0/24", "198.51.100.0/24"]
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
                "execute-api:/*"
            ]
        },
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
                "execute-api:/*"
            ],
            "Condition" : {
                "StringNotEquals": {
                   "aws:SourceVpc": "vpc-1a2b3c4d"
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
                "execute-api:/*"
            ]
        },
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
                "execute-api:/*"
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