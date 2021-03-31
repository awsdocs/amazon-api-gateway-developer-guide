# How Amazon API Gateway works with IAM<a name="security_iam_service-with-iam"></a>

Before you use IAM to manage access to API Gateway, you should understand what IAM features are available to use with API Gateway\. To get a high\-level view of how API Gateway and other AWS services work with IAM, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) in the *IAM User Guide*\.

**Topics**
+ [API Gateway identity\-based policies](#security_iam_service-with-iam-id-based-policies)
+ [API Gateway resource\-based policies](#security_iam_service-with-iam-resource-based-policies)
+ [Authorization based on API Gateway tags](#security_iam_service-with-iam-tags)
+ [API Gateway IAM roles](#security_iam_service-with-iam-roles)

## API Gateway identity\-based policies<a name="security_iam_service-with-iam-id-based-policies"></a>

With IAM identity\-based policies, you can specify which actions and resources are allowed or denied as well as the conditions under which actions are allowed or denied\. API Gateway [supports specific actions, resources, and condition keys](https://docs.aws.amazon.com/service-authorization/latest/reference/list_manageamazonapigateway.html#manageamazonapigateway-policy-keys)\. For information about all of the elements that you use in a JSON policy, see [IAM JSON Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\.

The following example shows an identity\-based policy that allows a user to create or update only private REST APIs\. For more examples, see [Amazon API Gateway identity\-based policy examples](security_iam_id-based-policy-examples.md)\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ScopeToPrivateApis",
      "Effect": "Allow",
      "Action": [
        "apigateway:PATCH",
        "apigateway:POST",
        "apigateway:PUT"
      ],
      "Resource": [
        "arn:aws:apigateway:us-east-1::/restapis",
        "arn:aws:apigateway:us-east-1::/restapis/??????????"
      ],
      "Condition": {
        "ForAnyValue:StringEqualsIfExists: {
          "apigateway:Request/EndpointType": "PRIVATE",
          "apigateway:Resource/EndpointType": "PRIVATE"
        }
      }
    },
    {
      "Sid": "AllowResourcePolicyUpdates",
      "Effect": "Allow",
      "Action": [
          "apigateway:UpdateRestApiPolicy"
      ],
      "Resource": [
          "arn:aws:apigateway:us-east-1::/restapis/*"
      ]
    }
  ]
}
```

### Actions<a name="security_iam_service-with-iam-id-based-policies-actions"></a>

The `Action` element of a JSON policy describes the actions that you can use to allow or deny access in a policy\.

Policy actions in API Gateway use the following prefix before the action: `apigateway:`\. Policy statements must include either an `Action` or `NotAction` element\. API Gateway defines its own set of actions that describe tasks that you can perform with this service\.



The API\-managing `Action` expression has the format `apigateway:action`, where *action* is one of the following API Gateway actions: **GET**, **POST**, **PUT**, **DELETE**, **PATCH** \(to update resources\), or **\***, which is all of the previous actions\.

Some examples of the `Action` expression include:
+ **apigateway:\*** for all API Gateway actions\.
+ **apigateway:GET** for just the GET action in API Gateway\.

To specify multiple actions in a single statement, separate them with commas as follows:

```
"Action": [
      "apigateway:action1",
      "apigateway:action2"
```

For information about HTTP verbs to use for specific API Gateway operations, see [Amazon API Gateway Version 1 API Reference](https://docs.aws.amazon.com/apigateway/api-reference/) \(REST APIs\) and [Amazon API Gateway Version 2 API Reference](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/api-reference.html) \(WebSocket and HTTP APIs\)\.

For more information, see [Amazon API Gateway identity\-based policy examples](security_iam_id-based-policy-examples.md)\.

### Resources<a name="security_iam_service-with-iam-id-based-policies-resources"></a>

Administrators can use AWS JSON policies to specify who has access to what\. That is, which **principal** can perform **actions** on what **resources**, and under what **conditions**\.

The `Resource` JSON policy element specifies the object or objects to which the action applies\. Statements must include either a `Resource` or a `NotResource` element\. As a best practice, specify a resource using its [Amazon Resource Name \(ARN\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\. You can do this for actions that support a specific resource type, known as *resource\-level permissions*\.

For actions that don't support resource\-level permissions, such as listing operations, use a wildcard \(\*\) to indicate that the statement applies to all resources\.

```
"Resource": "*"
```



API Gateway resources have the following ARN format:

```
arn:aws:apigateway:region::resource-path-specifier
```

For example, to specify a REST API with the id *`api-id`* and its sub\-resources, such as authorizers in your statement, use the following ARN:

```
"Resource": "arn:aws:apigateway:us-east-2::/restapis/api-id/*"
```

To specify all REST APIs and sub\-resources that belong to a specific account, use the wildcard \(\*\):

```
"Resource": "arn:aws:apigateway:us-east-2::/restapis/*"
```

For a list of API Gateway resource types and their ARNs, see [API Gateway Amazon Resource Name \(ARN\) reference](arn-format-reference.md)\. 

### Condition keys<a name="security_iam_service-with-iam-id-based-policies-conditionkeys"></a>

Administrators can use AWS JSON policies to specify who has access to what\. That is, which **principal** can perform **actions** on what **resources**, and under what **conditions**\.

The `Condition` element \(or `Condition` *block*\) lets you specify conditions in which a statement is in effect\. The `Condition` element is optional\. You can create conditional expressions that use [condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html), such as equals or less than, to match the condition in the policy with values in the request\. 

If you specify multiple `Condition` elements in a statement, or multiple keys in a single `Condition` element, AWS evaluates them using a logical `AND` operation\. If you specify multiple values for a single condition key, AWS evaluates the condition using a logical `OR` operation\. All of the conditions must be met before the statement's permissions are granted\.

 You can also use placeholder variables when you specify conditions\. For example, you can grant an IAM user permission to access a resource only if it is tagged with their IAM user name\. For more information, see [IAM policy elements: variables and tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html) in the *IAM User Guide*\. 

AWS supports global condition keys and service\-specific condition keys\. To see all AWS global condition keys, see [AWS global condition context keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.

API Gateway defines its own set of condition keys and also supports using some global condition keys\. For a list of API Gateway condition keys, see [Condition Keys for Amazon API Gateway](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_manageamazonapigateway.html#manageamazonapigateway-policy-keys) in the *IAM User Guide*\. For information about which actions and resources you can use with a condition key, see [Actions Defined by Amazon API Gateway](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_manageamazonapigateway.html#amazonapigateway-actions-as-permissions)\.

For information about tagging, including tag\-based access control, see [Tagging your API Gateway resources](apigateway-tagging.md)\.

### Examples<a name="security_iam_service-with-iam-id-based-policies-examples"></a>



For examples of API Gateway identity\-based policies, see [Amazon API Gateway identity\-based policy examples](security_iam_id-based-policy-examples.md)\.

## API Gateway resource\-based policies<a name="security_iam_service-with-iam-resource-based-policies"></a>

Resource\-based policies are JSON policy documents that specify what actions a specified principal can perform on the API Gateway resource and under what conditions\. API Gateway supports resource\-based permissions policies for REST APIs\. You use resource policies to control who can invoke a REST API\. For more information, see [Controlling access to an API with API Gateway resource policies](apigateway-resource-policies.md)\. 

### Examples<a name="security_iam_service-with-iam-resource-based-policies-examples"></a>

For examples of API Gateway resource\-based policies, see [API Gateway resource policy examples](apigateway-resource-policies-examples.md)\.

## Authorization based on API Gateway tags<a name="security_iam_service-with-iam-tags"></a>

You can attach tags to API Gateway resources or pass tags in a request to API Gateway\. To control access based on tags, you provide tag information in the [condition element](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) of a policy using the `apigateway:ResourceTag/key-name`, `aws:RequestTag/key-name`, or `aws:TagKeys` condition keys\. For more information about tagging API Gateway resources, see [Using tags to control access to API Gateway resources](apigateway-tagging-iam-policy.md)\.

For an examples of identity\-based policies for limiting access to a resource based on the tags on that resource, see [Using tags to control access to API Gateway resources](apigateway-tagging-iam-policy.md)\.

## API Gateway IAM roles<a name="security_iam_service-with-iam-roles"></a>

An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an entity within your AWS account that has specific permissions\.

### Using temporary credentials with API Gateway<a name="security_iam_service-with-iam-roles-tempcreds"></a>

You can use temporary credentials to sign in with federation, assume an IAM role, or to assume a cross\-account role\. You obtain temporary security credentials by calling AWS STS API operations such as [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html) or [GetFederationToken](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetFederationToken.html)\. 

API Gateway supports using temporary credentials\. 

### Service\-linked roles<a name="security_iam_service-with-iam-roles-service-linked"></a>

[Service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role) allow AWS services to access resources in other services to complete an action on your behalf\. Service\-linked roles appear in your IAM account and are owned by the service\. An IAM administrator can view but not edit the permissions for service\-linked roles\.

API Gateway supports service\-linked roles\. For information about creating or managing API Gateway service\-linked roles, see [Using service\-linked roles for API Gateway](using-service-linked-roles.md)\.

### Service roles<a name="security_iam_service-with-iam-roles-service"></a>

A service can assume a [service role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-role) on your behalf\. A service role allows the service to access resources in other services to complete an action on your behalf\. Service roles appear in your IAM account and are owned by the account, so an IAM administrator can change the permissions for this role\. However, doing so might break the functionality of the service\.

API Gateway supports service roles\. 

### <a name="security_iam_service-with-iam-roles-choose"></a>