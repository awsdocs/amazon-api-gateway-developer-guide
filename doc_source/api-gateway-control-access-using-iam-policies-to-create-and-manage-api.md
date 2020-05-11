# Control access for managing an API<a name="api-gateway-control-access-using-iam-policies-to-create-and-manage-api"></a>

 In this section, you will learn how to create IAM policy statements to control who can or cannot create, deploy and update an API in API Gateway\. You'll also find the policy statements reference, including the formats of the `Action` and `Resource` fields related to the API managing service\. 

## Control who can create and manage an API Gateway API with IAM policies<a name="api-gateway-control-access-using-iam-policies"></a>

 To control who can or cannot create, deploy and update your API using the API managing service of API Gateway, create an IAM policy document with required permissions as shown in the following policy template: 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Permission",
      "Action": [
        "apigateway:HTTP_VERB"
      ],
      "Resource": [
        "arn:aws:apigateway:region::resource1-path",
        "arn:aws:apigateway:region::resource2-path",
        ...
      ]
    }
  ]
}
```

 Here, `Permission` can be `Allow` or `Deny` to grant or revoke, respectively, the access rights as stipulated by the policy statement\. For more information, see [AWS IAM permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions.html)\.

`HTTP_VERB` can be any of the [ API Gateway\-supported HTTP verbs](#api-gateway-iam-policy-action-format-for-managing-api)\. `*` can be used to denote any of the HTTP verbs\. 

 `Resource` contains a list of ARNs of the affected API entities, including [RestApi](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/), [Resource](https://docs.aws.amazon.com/apigateway/api-reference/resource/resource/), [Method](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/), [Integration](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/), [DocumentationPart](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/), [Model](https://docs.aws.amazon.com/apigateway/api-reference/resource/model/), [Authorizer](https://docs.aws.amazon.com/apigateway/api-reference/resource/authorizer/), [UsagePlan](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/), etc\. For more information, see [Resource format of permissions for managing API in API Gateway](#api-gateway-iam-policy-resource-format-for-managing-api)\. 

By combining different policy statements, you can customize the access permissions for individual users, groups or roles to access selected API entities and to perform specified actions against those entities\. For example, you can include the following statement in the IAM policy to grant your documentation team permission to create, publish, update and delete the [documentation parts](https://docs.aws.amazon.com/apigateway/api-reference/resource/documentation-part/) of a specified API as well as to view the API entities\. 

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
        "arn:aws:apigateway:region::/restapis/api-id/*"
      ] 
    },
    {
      "Effect": "Allow",
      "Action": [
        "apigateway:POST",
        "apigateway:PATCH",
        "apigateway:DELETE"
      ],
      "Resource": [
        "arn:aws:apigateway:region::/restapis/api-id/documentation/*"
      ]
    }   
  ]
}
```

 For your API core development team who is responsible for all operations, you can include the following statement in the IAM policy to grant the team much broader access permissions\.

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
        "arn:aws:apigateway:*::/*"
      ]
    }
  ]
}
```

## Statement reference of IAM policies for managing API in API Gateway<a name="api-gateway-iam-policy-statement-reference-for-managing-api"></a>

 The following information describes the `Action` and `Resource` element format used in an IAM policy statement to grant or revoke permissions for managing API Gateway API entities\. 

### Action format of permissions for managing API in API Gateway<a name="api-gateway-iam-policy-action-format-for-managing-api"></a>

The API\-managing `Action` expression has the following general format:

```
apigateway:action
```

where *action* is one of the following API Gateway actions:
+ **\***, which represents all of the following actions\.
+ **GET**, which is used to get information about resources\.
+ **POST**, which is primarily used to create child resources\.
+ **PUT**, which is primarily used to update resources \(and, although not recommended, can be used to create child resources\)\.
+ **DELETE**, which is used to delete resources\.
+ **PATCH**, which can be used to update resources\.

Some examples of the `Action` expression include:
+ **apigateway:\*** for all API Gateway actions\.
+ **apigateway:GET** for just the GET action in API Gateway\.

### Resource format of permissions for managing API in API Gateway<a name="api-gateway-iam-policy-resource-format-for-managing-api"></a>

The API\-managing `Resource` expression has the following general format:

```
arn:aws:apigateway:region::resource-path-specifier
```

where *region* is a target AWS region \(such as **us\-east\-1** or **\*** for all supported AWS regions\), and *resource\-path\-specifier* is the path to the target resources\.

Some example resource expressions include:
+ **arn:aws:apigateway:*region*::/restapis/\*** for all resources, methods, models, and stages in the AWS region of *region*\.
+ **arn:aws:apigateway:*region*::/restapis/*api\-id*/\*** for all resources, methods, models, and stages in the API with the identifier of *api\-id* in the AWS region of *region*\.
+ **arn:aws:apigateway:*region*::/restapis/*api\-id*/resources/*resource\-id*/\*** for all resources and methods in the resource with the identifier *resource\-id*, which is in the API with the identifier of *api\-id* in the AWS region of *region*\.
+ **arn:aws:apigateway:*region*::/restapis/*api\-id*/resources/*resource\-id*/methods/\*** for all of the methods in the resource with the identifier *resource\-id*, which is in the API with the identifier of *api\-id* in the AWS region of *region*\.
+ **arn:aws:apigateway:*region*::/restapis/*api\-id*/resources/*resource\-id*/methods/GET** for just the GET method in the resource with the identifier *resource\-id*, which is in the API with the identifier of *api\-id* in the AWS region of *region*\.
+ **arn:aws:apigateway:*region*::/restapis/*api\-id*/models/\*** for all of the models in the API with the identifier of *api\-id* in the AWS region of *region*\.
+ **arn:aws:apigateway:*region*::/restapis/*api\-id*/models/*model\-name*** for the model with the name of *model\-name*, which is in the API with the identifier of *api\-id* in the AWS region of *region*\.
+ **arn:aws:apigateway:*region*::/restapis/*api\-id*/stages/\*** for all of the stages in the API with the identifier of *api\-id* in the AWS region of *region*\.
+ **arn:aws:apigateway:*region*::/restapis/*api\-id*/stages/*stage\-name*** for just the stage with the name of *stage\-name* in the API with the identifier of *api\-id* in the AWS region of *region*\.

To learn more, see [API Gateway Amazon Resource Name \(ARN\) reference](arn-format-reference.md)\.