# x\-amazon\-apigateway\-policy<a name="openapi-extensions-policy"></a>

Specifies a resource policy for a REST API\. To learn more about resource policies, see [Controlling access to an API with API Gateway resource policies](apigateway-resource-policies.md)\. For resource policy examples, see [API Gateway resource policy examples](apigateway-resource-policies-examples.md)\.

## `x-amazon-apigateway-policy` example<a name="openapi-extensions-policy-example"></a>

The following example specifies a resource policy for a REST API\. The resource policy denies \(blocks\) incoming traffic to an API from a specified source IP address block\. On import, `"execute-api:/*"` is converted to `arn:aws:execute-api:region:account-id:api-id/*`, using the current Region, your AWS account ID, and the current REST API ID\.

```
"x-amazon-apigateway-policy": {
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
                    "aws:SourceIp": "192.0.2.0/24"
                }
            }
        }
    ]
}
```