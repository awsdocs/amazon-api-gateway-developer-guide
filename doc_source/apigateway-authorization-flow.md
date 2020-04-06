# How API Gateway resource policies affect authorization workflow<a name="apigateway-authorization-flow"></a>

When API Gateway evaluates the resource policy attached to your API, the result is affected by the authentication type that you have defined for the API, as illustrated in the flowcharts in the following sections\.

**Topics**
+ [API Gateway resource policy only](#apigateway-authorization-flow-resource-policy-only)
+ [Lambda authorizer and resource policy](#apigateway-authorization-flow-lambda)
+ [IAM authentication and resource policy](#apigateway-authorization-flow-iam)
+ [Amazon Cognito authentication and resource policy](#apigateway-authorization-flow-cognito)
+ [Policy evaluation outcome tables](#apigateway-resource-policies-iam-policies-interaction)

## API Gateway resource policy only<a name="apigateway-authorization-flow-resource-policy-only"></a>

In this workflow, an API Gateway resource policy is attached to the API, but no authentication type is defined for the API\. Evaluation of the policy involves seeking an explicit allow based on the inbound criteria of the caller\. An implicit denial or any explicit denial results in denying the caller\.

The following is an example of such a resource policy\.

![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-auth-resource-policy-only.png)

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": "arn:aws:execute-api:region:account-id:api-id/",
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": ["192.0.2.0/24", "198.51.100.0/24" ]
                }
            }
        }
    ]
}
```

## Lambda authorizer and resource policy<a name="apigateway-authorization-flow-lambda"></a>

In this workflow, a Lambda authorizer is configured for the API in addition to a resource policy\. The resource policy is evaluated in two phases\. Before calling the Lambda authorizer, API Gateway first evaluates the policy and checks for any explicit denials\. If found, the caller is denied access immediately\. Otherwise, the Lambda authorizer is called, and it returns a [policy document](api-gateway-lambda-authorizer-output.md), which is evaluated in conjunction with the resource policy\. The result is determined based on [Table A](#apigateway-resource-policies-iam-policies-interaction) \(near the end of this topic\)\.

The following example resource policy allows calls only from the VPC endpoint whose VPC endpoint ID is `vpce-1a2b3c4d`\. During the "pre\-auth" evaluation, only the calls coming from the VPC endpoint indicated in the example are allowed to move forward and evaluate the Lambda authorizer\. All remaining calls are blocked\.

![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-auth-lambda-resource-policy.png)

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:region:account-id:api-id/"
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

## IAM authentication and resource policy<a name="apigateway-authorization-flow-iam"></a>

In this workflow, IAM authentication is configured for the API in addition to a resource policy\. After authenticating the user with the IAM service, the policies attached to the IAM user in addition to the resource policy are evaluated together\. The outcome varies based on whether the caller is in the same account, or a separate AWS account, from the API owner\. 

If the caller and API owner are from separate accounts, both the IAM user policies and the resource policy explicitly allow the caller to proceed\. \(See [Table B](#apigateway-resource-policies-iam-policies-interaction) at the end of this topic\.\) In contrast, if the caller and the API owner are in the same account, then either the user policies or the resource policy must explicitly allow the caller to proceed\. \(See [Table A](#apigateway-resource-policies-iam-policies-interaction) below\.\)

![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-auth-iam-resource-policy.png)

The following is an example of a cross\-account resource policy\. Assuming the IAM user policy contains an allow, this resource policy allows calls only from the VPC whose VPC ID is `vpc-2f09a348`\. \(See [Table B](#apigateway-resource-policies-iam-policies-interaction) at the end of this topic\.\)

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "",
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:region:account-id:api-id/"
            ],
            "Condition" : {
                "StringEquals": {
                    "aws:SourceVpc": "vpc-2f09a348"
                    }
            }
        }
    ]
}
```

## Amazon Cognito authentication and resource policy<a name="apigateway-authorization-flow-cognito"></a>

In this workflow, an [Amazon Cognito user pool](apigateway-integrate-with-cognito.md) is configured for the API in addition to a resource policy\. API Gateway first attempts to authenticate the caller through Amazon Cognito\. This is typically performed through a [JWT token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) that is provided by the caller\. If authentication is successful, the resource policy is evaluated independently, and an explicit allow is required\. A deny or "neither allow or deny" results in a deny\. The following is an example of a resource policy that might be used together with Amazon Cognito user pools\.

![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-auth-cognito-resource-policy.png)

The following is an example of a resource policy that allows calls only from specified source IPs, assuming that the Amazon Cognito authentication token contains an allow\. \(See [Table A](#apigateway-resource-policies-iam-policies-interaction) near the end of this topic\.\)

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": "arn:aws:execute-api:region:account-id:api-id/",
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": ["192.0.2.0/24", "198.51.100.0/24" ]
                }
            }
        }
    ]
}
```

## Policy evaluation outcome tables<a name="apigateway-resource-policies-iam-policies-interaction"></a>

Table A lists the resulting behavior when access to an API Gateway API is controlled by an IAM policy \(or a Lambda or Amazon Cognito user pools authorizer\) and an API Gateway resource policy, both of which are in the same AWS account\.


**Table A: Account A Calls API Owned by Account A**  

| IAM user policy \(or Lambda or Amazon Cognito user pools authorizer\) | API Gateway resource policy | Resulting behavior | 
| --- | --- | --- | 
| Allow | Allow | Allow | 
| Allow | Neither Allow nor Deny | Allow | 
| Allow | Deny | Explicit Deny | 
| Neither Allow nor Deny | Allow | Allow | 
| Neither Allow nor Deny | Neither Allow nor Deny | Implicit Deny | 
| Neither Allow nor Deny | Deny | Explicit Deny | 
| Deny | Allow | Explicit Deny | 
| Deny | Neither Allow nor Deny | Explicit Deny | 
| Deny | Deny | Explicit Deny | 

Table B lists the resulting behavior when access to an API Gateway API is controlled by an IAM policy \(or a Lambda or Amazon Cognito user pools authorizer\) and an API Gateway resource policy, which are in different AWS accounts\. If either is silent \(neither allow nor deny\), cross\-account access is denied\. This is because cross\-account access requires that both the resource policy and the IAM policy \(or a Lambda or Amazon Cognito user pools authorizer\) explicitly grant access\.


**Table B: Account B Calls API Owned by Account A**  

| IAM user policy \(or Lambda or Amazon Cognito user pools authorizer\) | API Gateway resource policy | Resulting behavior | 
| --- | --- | --- | 
| Allow | Allow | Allow | 
| Allow | Neither Allow nor Deny | Implicit Deny | 
| Allow | Deny | Explicit Deny | 
| Neither Allow nor Deny | Allow | Implicit Deny | 
| Neither Allow nor Deny | Neither Allow nor Deny | Implicit Deny | 
| Neither Allow nor Deny | Deny | Explicit Deny | 
| Deny | Allow | Explicit Deny | 
| Deny | Neither Allow nor Deny | Explicit Deny | 
| Deny | Deny | Explicit Deny | 