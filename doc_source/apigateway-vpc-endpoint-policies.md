# Use VPC endpoint policies for private APIs in API Gateway<a name="apigateway-vpc-endpoint-policies"></a>

You can improve the security of your [private APIs](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-private-apis.html) by configuring API Gateway to use an [interface VPC endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html)\. Interface endpoints are powered by AWS PrivateLink, a technology that enables you to privately access AWS services by using private IP addresses\. For more information about creating VPC endpoints, see [Creating an Interface Endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint)\.

A *VPC endpoint policy* is an IAM resource policy that you can attach to an interface VPC endpoint to control access to the endpoint\. For more information, see [Controlling Access to Services with VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html)\. You can use an endpoint policy to restrict the traffic going from your internal network to access your private APIs\. You can choose to allow or disallow access to specific private APIs that can be accessed through the VPC endpoint\.

VPC endpoint policies can be used together with API Gateway resource policies\. The resource policy is used to specify which principals can access the API\. The endpoint policy specifies which private APIs can be called via the VPC endpoint\. For more information about resource policies, see [Controlling access to an API with API Gateway resource policies](apigateway-resource-policies.md)\.

## VPC endpoint policy examples<a name="apigateway-vpc-endpoint-policies-examples"></a>

You can create policies for Amazon Virtual Private Cloud endpoints for Amazon API Gateway in which you can specify:
+ The principal that can perform actions\.
+ The actions that can be performed\.
+ The resources that can have actions performed on them\.

To attach the policy to the VPC endpoint, you'll need to use the VPC console\. For more information, see [Controlling Access to Services with VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html)\.

## Example 1: VPC endpoint policy granting access to two APIs<a name="apigateway-vpc-endpoint-policies-example-1"></a>

The following example policy grants access to only two specific APIs via the VPC endpoint that the policy is attached to\.

```
{
    "Statement": [
        {
            "Principal": "*",
            "Action": [
                "execute-api:Invoke"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:execute-api:us-east-1:123412341234:a1b2c3d4e5/*",
                "arn:aws:execute-api:us-east-1:123412341234:aaaaa11111/*"
            ]
        }
    ]
}
```

## Example 2: VPC endpoint policy granting access to GET methods<a name="apigateway-vpc-endpoint-policies-example-2"></a>

The following example policy grants users access to `GET` methods for a specific API via the VPC endpoint that the policy is attached to\.

```
{
    "Statement": [
        {
            "Principal": "*",
            "Action": [
                "execute-api:Invoke"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:execute-api:us-east-1:123412341234:a1b2c3d4e5/stageName/GET/*"
            ]
        }
    ]
}
```

## Example 3: VPC endpoint policy granting a specific user access to a specific API<a name="apigateway-vpc-endpoint-policies-example-3"></a>

The following example policy grants a specific user access to a specific API via the VPC endpoint that the policy is attached to\.

```
{
    "Statement": [
        {
            "Principal": {
                "AWS": [
                    "arn:aws:iam::123412341234:user/MyUser"
                ]
            },
            "Action": [
                "execute-api:Invoke"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:execute-api:us-east-1:123412341234:a1b2c3d4e5/*"
            ]
        }
    ]
}
```