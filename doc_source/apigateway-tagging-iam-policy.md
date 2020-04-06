# Using tags to control access to API Gateway resources<a name="apigateway-tagging-iam-policy"></a>

Conditions in AWS Identity and Access Management policies are part of the syntax that you use to specify permissions to API Gateway resources\. For details about specifying IAM policies, see [Control access to an API with IAM permissions](permissions.md)\. In API Gateway, resources can have tags, and some actions can include tags\. When you create an IAM policy, you can use tag condition keys to control:
+ Which users can perform actions on an API Gateway resource, based on tags that the resource already has\.
+ Which tags can be passed in an action's request\.
+ Whether specific tag keys can be used in a request\.

Using tag\-based access control can allow for finer control than API\-level control, as well as more dynamic control than resource\-based access control\. IAM policies can be created that allow or disallow an operation based on tags provided in the request \(request tags\), or tags on the resource that is being operated on \(resource tags\)\. In general, resource tags are for resources that already exist\. Request tags are for when you're creating new resources\.

For the complete syntax and semantics of tag condition keys, see [Controlling Access Using Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *IAM User Guide*\.

The following examples demonstrate how to specify tag conditions in policies for API Gateway users\.

## Example 1: Limit actions based on resource tags<a name="apigateway-tagging-iam-policy-example-1"></a>

The following example policy grants users permission to perform `GET` actions on all resources\. In addition, if a resource has a tag named `iamrole` with a value of `readWrite`, the policy grants users permission to perform all actions on the resource\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "apigateway:GET",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "apigateway:*",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/iamrole": "readWrite"
        }
      }
    }
  ]
}
```

## Example 2: Limit actions based on tags in the request<a name="apigateway-tagging-iam-policy-example-2"></a>

The following example policy specifies that:
+ When the user creates a new stage, the request to create the stage must contain a tag named `stage`\.
+ The value of the `stage` tag must be `beta`, `gamma`, or `prod`\. Otherwise, the request to create the stage is denied\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "apigateway:*"
      ],
      "Resource": [
        "*"
      ]
    },
    {
      "Effect": "Deny",
      "Action": "apigateway:POST",
      "Resource": "arn:aws:apigateway:*::/restapis/*/stages",
      "Condition": {
        "Null": {
          "aws:RequestTag/stage": "true"
        }
      }
    },
    {
      "Effect": "Deny",
      "Action": "apigateway:POST",
      "Resource": "arn:aws:apigateway:*::/restapis/*/stages",
      "Condition": {
        "ForAnyValue:StringNotEquals": {
          "aws:RequestTag/stage": [
            "beta",
            "gamma",
            "prod"
          ]
        }
      }
    }
  ]
}
```

## Example 3: Deny actions based on resource tags<a name="apigateway-tagging-iam-policy-example-3"></a>

The following example policy allows users to perform all actions on API Gateway resources by default\. If a resource has a tag named `stage` with a value of `prod`, users are denied permission to perform modifications \(`PATCH`, `PUT`, `POST`, `DELETE`\) on the resource\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "apigateway:*",
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": [
        "apigateway:PATCH",
        "apigateway:PUT",
        "apigateway:POST",
        "apigateway:DELETE"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/stage": "prod"
        }
      }
    }
  ]
}
```

## Example 4: Allow actions based on resource tags<a name="apigateway-tagging-iam-policy-example-4"></a>

The following example policy allows users to perform all actions on all API Gateway resources by default\. If a resource has a tag named `environment` whose value is `prod`, users aren't allowed to perform any operations on the resource\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "apigateway:*",
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": [
        "apigateway:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/environment": "prod"
        }
      }
    }
  ]
}
```

## Example 5: Allow actions based on resource tag keys<a name="apigateway-tagging-iam-policy-example-5"></a>

The following example policy allows users to perform all actions on all API Gateway resources by default\. If a resource has a tag named `prod` with any value, including an empty string, users aren't allowed to perform operations that modify the resource\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "apigateway:*",
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": [
        "apigateway:PATCH",
        "apigateway:PUT",
        "apigateway:POST",
        "apigateway:DELETE"
      ],
      "Resource": "*",
      "Condition": {
        "Null": {
          "aws:ResourceTag/prod": "false"
        }
      }
    }
  ]
}
```