# IAM policy examples for managing API Gateway APIs<a name="api-gateway-iam-policy-examples"></a>

The following example policy documents shows various use cases to set access permissions for managing API resources in API Gateway\. For permissions model and other background information, see [ Control who can create and manage an API Gateway API with IAM policies](api-gateway-control-access-using-iam-policies-to-create-and-manage-api.md#api-gateway-control-access-using-iam-policies)\. 

**Topics**
+ [Simple read permissions](#api-gateway-policy-example-apigateway-general)
+ [Read\-only permissions on any APIs](#api-gateway-policy-example-apigateway-readonly)
+ [Full access permissions for any API Gateway resources](#api-gateway-policy-example-apigateway-full-access)
+ [Full\-access permissions for managing API stages](#api-gateway-policy-example-apigateway-stage-full-access)
+ [Block specified users from deleting any API resources](#api-gateway-policy-example-apigateway-blacklist-user-for-delete)

## Simple read permissions<a name="api-gateway-policy-example-apigateway-general"></a>

The following policy statement gives the user permission to get information about all of the resources, methods, models, and stages in the API with the identifier of `a123456789` in the AWS region of us\-east\-1:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "apigateway:GET"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/restapis/a123456789/*"
      ]
    }
  ]
}
```

The following example policy statement gives the IAM user permission to list information for all resources, methods, models, and stages in any region\. The user also has permission to perform all available API Gateway actions for the API with the identifier of `a123456789` in the AWS region of us\-east\-1:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "apigateway:GET"
      ],
      "Resource": [
        "arn:aws:apigateway:*::/restapis/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "apigateway:*"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/restapis/a123456789/*"
      ]
    }
  ]
}
```

## Read\-only permissions on any APIs<a name="api-gateway-policy-example-apigateway-readonly"></a>

The following policy document will permit attached entities \(users, groups or roles\) to retrieve any of the APIs of the caller's AWS account\. This includes any of the child resources of an API, such as method, integration, etc\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1467321237000",
            "Effect": "Deny",
            "Action": [
                "apigateway:POST",
                "apigateway:PUT",
                "apigateway:PATCH",
                "apigateway:DELETE"
            ],
            "Resource": [
                "arn:aws:apigateway:us-east-1::/*"
            ]
        },
        {
            "Sid": "Stmt1467321341000",
            "Effect": "Deny",
            "Action": [
                "apigateway:GET"
            ],
            "Resource": [
                "arn:aws:apigateway:us-east-1::/",
                "arn:aws:apigateway:us-east-1::/account",
                "arn:aws:apigateway:us-east-1::/clientcertificates",
                "arn:aws:apigateway:us-east-1::/domainnames",
                "arn:aws:apigateway:us-east-1::/apikeys"
            ]
        },
        {
            "Sid": "Stmt1467321344000",
            "Effect": "Allow",
            "Action": [
                "apigateway:GET"
            ],
            "Resource": [
                "arn:aws:apigateway:us-east-1::/restapis/*"
            ]
        }
    ]
}
```

The first `Deny` statement explicitly prohibits any calls of `POST`, `PUT`, `PATCH`, `DELETE` on any resources in API Gateway\. This ensures that such permissions will not be overridden by other policy documents also attached to the caller\. The second `Deny` statement blocks the caller to query the root \(`/`\) resource, account information \(`/account`\), client certificates \(`/clientcertificates`\), custom domain names \(`/domainnames`\) and API keys \(`/apikeys`\. Together, the three statements ensure that the caller can only query API\-related resources\. This can be useful in API testing when you do not want the tester to modify any of the code\.

To restrict the above read\-only access to specified APIs, replace the `Resource` property of `Allow` statement by the following:

```
"Resource":  ["arn:aws:apigateway:us-east-1::/restapis/restapi_id1/*", "arn:aws:apigateway:us-east-1::/restapis/restapi_id2/*"]
```

## Full access permissions for any API Gateway resources<a name="api-gateway-policy-example-apigateway-full-access"></a>

The following example policy document grants the full access to any of the API Gateway resource of the AWS account\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1467321765000",
            "Effect": "Allow",
            "Action": [
                "apigateway:*"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

In general, you should refrain from using such a broad and open access policy\. It may be necessary to do so for your API development core team so that they can create, deploy, update, and delete any API Gateway resources\.

## Full\-access permissions for managing API stages<a name="api-gateway-policy-example-apigateway-stage-full-access"></a>

The following example policy documents grants full\-access permissions on Stage related resources of any API in the caller's AWS account\.

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
                "arn:aws:apigateway:us-east-1::/restapis/*/stages",
                "arn:aws:apigateway:us-east-1::/restapis/*/stages/*"
            ]
        }
    ]
}
```

The above policy document grants full access permissions only to the `stages` collection and any of the contained `stage` resources, provided that no other policies granting more accesses have been attached to the caller\. Otherwise, you must explicitly deny all the other accesses\.

Using the above policy, caller must find out the REST API's identifier beforehand because the user cannot call `GET /restapis` to query the available APIs\. Also, if `arn:aws:apigateway:us-east-1::/restapis/*/stages` is not specified in the `Resource` list, the Stages resource becomes inaccessible\. In this case, the caller will not be able to create a stage nor get the existing stages, although he or she can still view, update or delete a stage, provided that he stage's name is known\.

To grant permissions for a specific API's stages, simply replace the `restapis/*` portion of the `Resource` specifications by `restapis/restapi_id`, where *restapi\_id* is the identifier of the API of interest\.

## Block specified users from deleting any API resources<a name="api-gateway-policy-example-apigateway-blacklist-user-for-delete"></a>

The following example IAM policy document blocks a specified user from deleting any API resources in API Gateway\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1467331998000",
            "Effect": "Allow",
            "Action": [
                "apigateway:GET",
                "apigateway:PATCH",
                "apigateway:POST",
                "apigateway:PUT"
            ],
            "Resource": [
                "arn:aws:apigateway:us-east-1::/restapis/*"
            ]
        },
        {
            "Sid": "Stmt1467332141000",
            "Effect": "Allow",
            "Action": [
                "apigateway:DELETE"
            ],
            "Condition": {
                "StringNotLike": {
                    "aws:username": "johndoe"
                }
            },
            "Resource": [
                "arn:aws:apigateway:us-east-1::/restapis/*"
            ]
        }
    ]
}
```

This IAM policy grants full access permission to create, deploy, update and delete API for attached users, groups or roles, except for the specified user \(`johndoe`\), who cannot delete any API resources\. It assumes that no other policy document granting `Allow` permissions on the root, API keys, client certificates or custom domain names has been attached to the caller\. 

To block the specified user from deleting specific API Gateway resources, e\.g\., a specific API or an API's resources, replace the `Resource` specification above by this: 

```
"Resource": ["arn:aws:apigateway:us-east-1::/restapis/restapi_id_1", "arn:aws:apigateway:us-east-1::/restapis/restapi_id_2/resources"]
```