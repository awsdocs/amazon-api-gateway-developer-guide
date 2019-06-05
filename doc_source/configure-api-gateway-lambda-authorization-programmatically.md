# Configure a Lambda Authorizer Using the AWS CLI or an AWS SDK<a name="configure-api-gateway-lambda-authorization-programmatically"></a>

You can also configure an API Gateway Lambda authorizer by using the AWS CLI or an AWS SDK\.

First, you first need to grant IAM principals in the account that owns your API permission to call [authorizer:create](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/authorizer-create) or [create\-authorizer](https://docs.aws.amazon.com/cli/latest/reference/apigateway/create-authorizer.html) to create the Lambda function used in your Lambda authorizer\. To grant this permission, you need to create the following IAM policy:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "apigateway:POST"
            ],
            "Resource": [
                "arn:aws:apigateway:region::/restapis/restapi_id/authorizers"
            ],
            //Create Authorizer operation is allowed only with the following Lambda function
            "Condition": {
                "StringEquals": {
                    "apigateway:AuthorizerUri": "arn:aws:lambda:region:account-id:function:lambda-function-name"
                }
            }
        }
    ]
}
```