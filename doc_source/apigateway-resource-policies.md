# Controlling Access to an API with API Gateway Resource Policies<a name="apigateway-resource-policies"></a>

Amazon API Gateway *resource policies* are JSON policy documents that you attach to an API to control whether a specified principal \(typically an IAM user or role\) can invoke the API\. You can use API Gateway resource policies to allow your API to be securely invoked by:
+ Users from a specified AWS account\.
+ Specified source IP address ranges or CIDR blocks\.
+ Specified virtual private clouds \(VPCs\) or VPC endpoints \(in any account\)\.

You can use resource policies for all API endpoint types in API Gateway: private, edge\-optimized, and Regional\.

For [private APIs](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-private-apis.html), you can use resource policies together with VPC endpoint policies to control which principals have access to which resources and actions\. For more information, see [Use VPC Endpoint Policies for Private APIs in API Gateway](apigateway-vpc-endpoint-policies.md)\.

You can attach a resource policy to an API by using the AWS Management Console, AWS CLI, or AWS SDKs\.

API Gateway resource policies are different from IAM policies\. IAM policies are attached to IAM entities \(users, groups, or roles\) and define what actions those entities are capable of doing on which resources\. API Gateway resource policies are attached to resources\. For a more detailed discussion of the differences between identity\-based \(IAM\) policies and resource policies, see [Identity\-Based Policies and Resource\-Based Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html)\.

You can use API Gateway resource policies together with IAM policies\.

**Topics**
+ [Access Policy Language Overview for Amazon API Gateway](apigateway-control-access-policy-language-overview.md)
+ [How API Gateway Resource Policies Affect Authorization Workflow](apigateway-authorization-flow.md)
+ [API Gateway Resource Policy Examples](apigateway-resource-policies-examples.md)
+ [Create and Attach an API Gateway Resource Policy to an API](apigateway-resource-policies-create-attach.md)
+ [AWS Condition Keys that can be used in API Gateway Resource Policies](apigateway-resource-policies-aws-condition-keys.md)