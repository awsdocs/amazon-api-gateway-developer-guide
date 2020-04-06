# Control cross\-account access to your API<a name="apigateway-control-cross-account-lambda-auth-and-integration"></a>

You can manage access to your APIs by creating IAM permission policies to control who can or cannot create, update, deploy, view, or delete API entities\. A policy is attached to an IAM user representing your user, to an IAM group containing the user, or to an IAM role assumed by the user\.

In the IAM policies you create for your APIs, you can use `Condition` elements to allow access only to certain Lambda integrations or authorizers\.

The `Condition` block uses boolean condition operators to match the condition in the policy against values in the request\. The `StringXxx` condition operator will work both for AWS integration \(in which the value should be a Lambda function ARN\) and Http integration \(in which the value should be an Http URI\)\. The following `StringXxx` condition operators are supported: `StringEquals`, `StringNotEquals`, `StringEqualsIgnoreCase`, `StringNotEqualsIgnoreCase`, `StringLike`, `StringNotLike`\. For more information, see [String Condition Operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_String) in the IAM User Guide\.

## IAM policy for cross\-account Lambda authorizer<a name="apigateway-control-cross-account-lambda-auth-policy"></a>

Here is an example of an IAM policy to control a cross\-account Lambda authorizer function:

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
                "arn:aws:apigateway:[region]::/restapis/restapi_id/authorizers"
            ],
            //Create Authorizer operation is allowed only with the following Lambda function
            "Condition": {
                "StringEquals": {
                    "apigateway:AuthorizerUri": "arn:aws:apigateway:region:lambda:path/2015-03-31/functions/arn:aws:lambda:region:123456789012:function:example/invocations"
                }
            }
        }
    ]
}
```

## IAM policy for cross\-account Lambda integration<a name="apigateway-control-cross-account-lambda-integration-policy"></a>

With cross\-account integration, in order to restrict operations on some specific resources \(such as `put-integration` for a specific Lambda function\), a `Condition` element can be added to the policy to specify which resource \(Lambda function\) is affected\.

Here is an example of an IAM policy to control a cross\-account Lambda integration function:

To grant another AWS acccount permission to call `[integration:put](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/integration-put)` or `[put\-integration](https://docs.aws.amazon.com/cli/latest/reference/apigateway/put-integration.html)` to set up a Lambda integration in your API, you can include the following statement in the IAM policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "apigateway:PUT"
            ],
            "Resource": [
                "arn:aws:apigateway:api-region::/restApis/api-id/resources/resource-id/methods/GET/integration"
            ],
            //PutIntegration is only valid with the following Lambda function
            "Condition": {
                "StringEquals": {
                    "apigateway:IntegrationUri": "arn:aws:lambda:region:account-id:function:lambda-function-name"
                }
            }
        }
    ]
}
```

## Allow another account to manage the Lambda function used when importing an OpenAPI file<a name="api-gateway-control-access-cross-account-lambda-swagger-import"></a>

To grant another AWS acccount permission to call `[restapi:import](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/)` or `[import\-rest\-api](https://docs.aws.amazon.com/cli/latest/reference/apigateway/import-rest-api.html)` to import an OpenAPI file, you can include the following statement in the IAM policy\.

In the `Condition` statement below, the string `"lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:account-id:function:lambda-function-name"` is the full ARN for the Lambda function\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "apigateway:POST"
            ],
            "Resource": "arn:aws:apigateway:*::/restapis",
            "Condition": {
                "StringLike": {
                    "apigateway:IntegrationUri": [
                        "arn:aws:apigateway:apigateway-region:lambda:path/2015-03-31/functions/arn:aws:lambda:lambda-region:account-id:function:lambda-function-name/invocations"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "apigateway:POST"
            ],
            "Resource": "arn:aws:apigateway:*::/restapis",
            "Condition": {
                "StringLike": {
                    "apigateway:AuthorizerUri": [
                        "arn:aws:apigateway:apigateway-region:lambda:path/2015-03-31/functions/arn:aws:lambda:lambda-region:account-id:function:lambda-function-name/invocations"
                    ]
                }
            }
        }
    ]
}
```