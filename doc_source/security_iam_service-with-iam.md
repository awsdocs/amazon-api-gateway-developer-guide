# How Amazon API Gateway works with IAM<a name="security_iam_service-with-iam"></a>

Before you use IAM to manage access to API Gateway, you should understand what IAM features are available to use with API Gateway\. To get a high\-level view of how API Gateway and other AWS services work with IAM, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) in the *IAM User Guide*\. For an overview of controlling and managing access to API Gateway APIs, see [Controlling and managing access to a REST API in API Gateway](apigateway-control-access-to-api.md)\.

**Topics**
+ [API Gateway identity\-based policies](#security_iam_service-with-iam-id-based-policies)
+ [API Gateway resource\-based policies](#security_iam_service-with-iam-resource-based-policies)
+ [Authorization based on API Gateway tags](#security_iam_service-with-iam-tags)
+ [API Gateway IAM roles](#security_iam_service-with-iam-roles)

## API Gateway identity\-based policies<a name="security_iam_service-with-iam-id-based-policies"></a>

With IAM identity\-based policies, you can specify allowed or denied actions and resources as well as the conditions under which actions are allowed or denied\. API Gateway supports specific actions, resources, and condition keys\. To learn about all of the elements that you use in a JSON policy, see [IAM JSON Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\.

### Actions<a name="security_iam_service-with-iam-id-based-policies-actions"></a>

The `Action` element of an IAM identity\-based policy describes the specific action or actions that will be allowed or denied by the policy\. Policy actions usually have the same name as the associated AWS API operation\. The action is used in a policy to grant permissions to perform the associated operation\. 

Policy actions in API Gateway use the following prefix before the action: `apigateway:`\. For example, to grant someone permission to create an API Gateway REST API, you include the `apigateway:POST` action in their policy\. Policy statements must include either an `Action` or `NotAction` element\. API Gateway defines its own set of actions that describe tasks that you can perform with this service\.

To specify multiple actions in a single statement, separate them with commas as follows:

```
"Action": [
      "apigateway:action1",
      "apigateway:action2"
```



To learn more, see [ Control access for managing an API](api-gateway-control-access-using-iam-policies-to-create-and-manage-api.md)\.

### Resources<a name="security_iam_service-with-iam-id-based-policies-resources"></a>

The `Resource` element specifies the object or objects to which the action applies\. Statements must include either a `Resource` or a `NotResource` element\. You specify a resource using an ARN or using the wildcard \(\*\) to indicate that the statement applies to all resources\.



API Gateway resources have the following ARN:

```
arn:aws:apigateway:region::resource-path-specifier
```

For more information about the format of ARNs, see [Amazon Resource Names \(ARNs\) and AWS Service Namespaces](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\.

For example, to specify a REST API with the id *`api-id`* in your statement, use the following ARN:

```
"Resource": "arn:aws:apigateway:us-east-2::/restapis/api-id/*"
```

To specify all REST APIs that belong to a specific account, use the wildcard \(\*\):

```
"Resource": "arn:aws:apigateway:us-east-2::/restapis/*"
```

To see a list of API Gateway resource types and their ARNs, see [API Gateway Amazon Resource Name \(ARN\) reference](arn-format-reference.md)\. To learn for which actions you can specify the ARN of each resource, see [Actions Defined by Amazon API Gateway](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonapigateway.html#amazonapigateway-actions-as-permissions)\.

### Condition keys<a name="security_iam_service-with-iam-id-based-policies-conditionkeys"></a>

The `Condition` element \(or `Condition` *block*\) lets you specify conditions in which a statement is in effect\. The `Condition` element is optional\. You can build conditional expressions that use [condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html), such as equals or less than, to match the condition in the policy with values in the request\. 

If you specify multiple `Condition` elements in a statement, or multiple keys in a single `Condition` element, AWS evaluates them using a logical `AND` operation\. If you specify multiple values for a single condition key, AWS evaluates the condition using a logical `OR` operation\. All of the conditions must be met before the statement's permissions are granted\.

 You can also use placeholder variables when you specify conditions\. For example, you can grant an IAM user permission to access a resource only if it is tagged with their IAM user name\. For more information, see [IAM Policy Elements: Variables and Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html) in the *IAM User Guide*\. 

API Gateway defines its own set of condition keys and also supports using some global condition keys\. To see all AWS global condition keys, see [AWS Global Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.



To see a list of API Gateway condition keys, see [Condition Keys for Amazon API Gateway](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonapigateway.html#amazonapigateway-policy-keys) in the *IAM User Guide*\. To learn which actions and resources you can use with a condition key, see [Actions Defined by Amazon API Gateway](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonapigateway.html#amazonapigateway-actions-as-permissions)\.

### Examples<a name="security_iam_service-with-iam-id-based-policies-examples"></a>



To view examples of API Gateway identity\-based policies, see [Amazon API Gateway identity\-based policy examples](security_iam_id-based-policy-examples.md)\.

## API Gateway resource\-based policies<a name="security_iam_service-with-iam-resource-based-policies"></a>

Resource\-based policies are JSON policy documents that specify what actions a specified principal can perform on the API Gateway resource and under what conditions\. API Gateway supports resource\-based permissions policies for REST APIs\. To learn more, see [Controlling access to an API with API Gateway resource policies](apigateway-resource-policies.md)\. 

### Examples<a name="security_iam_service-with-iam-resource-based-policies-examples"></a>



To view examples of API Gateway resource\-based policies, see [API Gateway resource policy examples](apigateway-resource-policies-examples.md)\.

## Authorization based on API Gateway tags<a name="security_iam_service-with-iam-tags"></a>

You can attach tags to API Gateway resources or pass tags in a request to API Gateway\. To control access based on tags, you provide tag information in the [condition element](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) of a policy using the `apigateway:ResourceTag/key-name`, `aws:RequestTag/key-name`, or `aws:TagKeys` condition keys\. For more information about tagging API Gateway resources, see [Using tags to control access to API Gateway resources](apigateway-tagging-iam-policy.md)\.

To view an example identity\-based policy for limiting access to a resource based on the tags on that resource, see [Using tags to control access to API Gateway resources](apigateway-tagging-iam-policy.md)\.

## API Gateway IAM roles<a name="security_iam_service-with-iam-roles"></a>

An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an entity within your AWS account that has specific permissions\.

### Using temporary credentials with API Gateway<a name="security_iam_service-with-iam-roles-tempcreds"></a>

You can use temporary credentials to sign in with federation, assume an IAM role, or to assume a cross\-account role\. You obtain temporary security credentials by calling AWS STS API operations such as [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html) or [GetFederationToken](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetFederationToken.html)\. 

API Gateway supports using temporary credentials\. 

### Service\-linked roles<a name="security_iam_service-with-iam-roles-service-linked"></a>

[Service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role) allow AWS services to access resources in other services to complete an action on your behalf\. Service\-linked roles appear in your IAM account and are owned by the service\. An IAM administrator can view but not edit the permissions for service\-linked roles\.

API Gateway supports service\-linked roles\. For details about creating or managing API Gateway service\-linked roles, see [Using service\-linked roles for API Gateway](using-service-linked-roles.md)\.

### Service roles<a name="security_iam_service-with-iam-roles-service"></a>

This feature allows a service to assume a [service role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-role) on your behalf\. This role allows the service to access resources in other services to complete an action on your behalf\. Service roles appear in your IAM account and are owned by the account\. This means that an IAM administrator can change the permissions for this role\. However, doing so might break the functionality of the service\.

API Gateway supports service roles\. 

### <a name="security_iam_service-with-iam-roles-choose"></a>